# EPIC5 2.1.6 release notes

Released **October 6, 2021**, commit ID **1981**, release name **Impignorate**. Tag `epic5-2.1.6` and release commit `1df4c951` confirm the identity and date. These are historical EPIC5 announcements; current EPIC6 qualifications appear where behavior has changed or the original description needs correction. See the [release index](release_notes.md).

## September 21, 2021: Clang static analysis

Another round of Clang static analysis identified issues that were fixed around this time.

## September 19, 2021: Reimplemented reconnect script

The release replaced `reconnect` with an implementation using improved reconnection facilities. It retained the original script as `reconnect.orig`, which users of that release could load with `/load reconnect.orig` to obtain the original behavior.

That filename is historical: current EPIC6 ships `reconnect` and `reconnect.old`, rather than `reconnect.orig`. See the current [reconnect script documentation](../scripts/reconnect.md) for its behavior and dependencies.

## September 11, 2021: Server presentation address

```text
$serverctl(GET <refnum> PADDR)
```

This returns the numeric address of the connected server peer, such as `1.2.3.4` for IPv4 or `2600::1` for IPv6. It describes the connection's remote address rather than performing a fresh hostname lookup. See [serverctl](../functions/serverctl.md).

## September 11, 2021: WINDOW CLEARREGEX

Requested by Zlonix, this operation removes existing retained lastlog entries whose text matches a regular expression:

```irc
/window clearregex annoybot
```

Removing the entries also requests a rebuild of the window's scrollback and display. This can retroactively remove an annoying bot's conversation. Current matching uses a case-insensitive extended regular expression; quote an expression containing spaces. It does not filter future messages or edit saved log files. See [window clearregex](../window/clearregex.md).

## September 11, 2021: RECONNECT_REQUIRED hook

The release introduced this event for situations requiring scripted reconnection intervention, initially a failed active server connection or a failed write to the server. Its argument is:

| Argument | Meaning |
| --- | --- |
| `$0` | Reference number of the server whose connection failed. |

Use the callback to save information about windows, channels, and other state in preparation for disconnection. Assume the connection has already been lost and that teardown is still in progress. **Do not reconnect, move windows, or manipulate server/channel ownership directly inside this hook.** Schedule recovery with `/defer` or a timer so that it runs after the failure handling finishes.

Current EPIC6 emits this hook on particular read/write failure paths involving a previously registered connection; it is not a notification for every connection attempt or failure. In particular, the write path also depends on `NO_FAIL_DISCONNECT` being off. Hook suppression does not itself reconnect or prevent the failure teardown. See [reconnect_required](../on/reconnect_required.md).

## September 8, 2021: WINDOW CLEARLEVEL

Also requested by Zlonix, this removes existing retained lastlog entries at the specified levels and rebuilds scrollback/display:

```irc
/window clearlevel joins,parts,quits,kicks
```

The comma-separated level mask is one argument. Omitting it does nothing. As with CLEARREGEX, this changes retained window history rather than saved log files or future message handling. See [window clearlevel](../window/clearlevel.md).

## September 8, 2021: Blocks with argument lists

The scripting language gained a statement consisting of an argument list followed by a block:

```text
(arglist) {block}
```

This can be viewed as an inline anonymous function: the argument list processes the current arguments, temporarily changes `$*`, and then runs the block. There are four caveats:

1. Named arguments become real local variables with the surrounding scope's ordinary lifetime. They persist after this statement finishes.
2. The change to `$*` lasts only for the statement. The previous positional arguments are restored afterward.
3. This construct is a statement, not a block. Wrap it in braces when supplying it where a block is expected.
4. Argument-list processing has a cost; processing arguments once outside a tight loop can avoid repeating that work.

For example, the original illustrated the required outer braces with:

```irc
if (# == 3) {(arg0, arg1, arg2) {... code ...}}
```

The `... code ...` portion is a placeholder for the body. See [argument lists](arglist.md) and [ircII syntax](ircii_syntax.md).

## September 7, 2021: Window refnum in CHANNEL_LOST

The hook gained the channel's window as its third argument:

| Argument | Meaning |
| --- | --- |
| `$0` | Server reference number. |
| `$1` | Channel name. |
| `$2` | User-visible reference number of the window containing the channel. |

The current callback occurs before that channel record is destroyed. This user-visible refnum differs from the internal window identifier supplied by CHANNEL_CLAIM. See [channel_lost](../on/channel_lost.md).

## September 6, 2021: JSON document functions

These functions had appeared earlier; this announcement documented them.

### Convert a JSON document into assigns

```text
$json_explode(varbase json-document)
```

The original example contained malformed JSON. With its punctuation corrected:

```text
$json_explode(e {"one": 1, "two": {"sub1": "hi", "sub2": "bye"}})
```

This creates assigns corresponding to:

| Expression | Value |
| --- | --- |
| `$e[one]` | `1` |
| `$e[two][sub1]` | `hi` |
| `$e[two][sub2]` | `bye` |

### Convert assigns into a JSON document

```text
$json_implode(varbase)
```

This serializes global assign descendants beneath `varbase` into JSON objects. It is not a lossless inverse of JSON_EXPLODE: current JSON_IMPLODE emits leaf values as strings, and neither function preserves all JSON type or array metadata. JSON_EXPLODE also normalizes object keys, does not clear old assigns first, and has an unhandled-`true` bug in current source. See [json_explode](../functions/json_explode.md) and [json_implode](../functions/json_implode.md) before relying on a round trip.

### Inspect errors

```text
$json_error()
```

The original broadly described this as reporting JSON errors. Current behavior is narrower: it exposes the last JSON_EXPLODE parse error, cleared by a successful parse. It is not a general status report for JSON_IMPLODE or every assignment/conversion failure. See [json_error](../functions/json_error.md).

## September 6, 2021: SSL handling improvements, second revision

This announcement replaced an earlier description after the certificate-handling design was revised. The following preserves its seven stages and documents current EPIC6 behavior. See [SSL connections](about_ssl_connections.md) for the detailed current reference.

### 1. Connect to an SSL server

```irc
/server irc.example.com:6697:type=irc-ssl
```

EPIC connects to the server and performs a TLS handshake using OpenSSL.

### 2. Complete the TLS handshake

A completed handshake establishes a TLS connection and supplies the peer certificate. EPIC must still apply its certificate acceptance policy before registering with IRC.

### 3. Collect certificate verification results

EPIC's OpenSSL verification callback records errors while allowing verification to continue. It groups the results into a depth-zero self-signed certificate error, a hostname mismatch, or any other error. Multiple categories may be present. The representative “most serious” error prioritizes other errors, then hostname mismatch, then the self-signed error.

These are the client's policy categories: describing self-signed or hostname errors as routine in the original announcement does not mean they establish a trusted server identity.

### 4. Invoke SSL_SERVER_CERT

EPIC initializes the server's `SSL_ACCEPT_CERT` value to `-1`, then invokes this hook:

| Argument | Meaning |
| --- | --- |
| `$0` | EPIC's virtual socket descriptor, not the server refnum. Use `$serverctl(FROM_SERVER)` to obtain the server refnum. |
| `$1` | URL-encoded certificate subject. |
| `$2` | URL-encoded certificate issuer. |
| `$3` | Public-key size in bits. |
| `$4` | Representative OpenSSL verification error: `0` means none, `18` is the depth-zero self-signed error, `62` is hostname mismatch, and other values identify other errors. |
| `$5` | Negotiated TLS protocol, such as `TLSv1.2`. |
| `$6` | Certificate hash; current source formats a SHA-1 digest as colon-separated hexadecimal bytes. |
| `$7` | Hostname-error flag: `0` or `1`. |
| `$8` | Self-signed-error flag: `0` or `1`. |
| `$9` | Other-error flag: `0` or `1`. |
| `$10` | Any-verification-error flag: `0` or `1`. |

The original said that setting `SSL_ACCEPT_CERT` to either `0` or `1` here supplied a final acceptance or rejection. **Current implementation does not enforce an early `0` as rejection:** any early value other than `-1` bypasses both the following policy evaluation and the rejection test inside that block. To reject through the normal policy path, leave this value at `-1` during SSL_SERVER_CERT and set it to `0` in SERVER_SSL_EVAL instead.

### 5. Make a provisional decision

If the early hook leaves `SSL_ACCEPT_CERT` at `-1`, EPIC applies this policy and stores its suggestion as `0` or `1`:

| Verification result | Provisional decision |
| --- | --- |
| No errors | Accept. |
| Only self-signed and/or hostname errors, with ACCEPT_INVALID_SSL_CERT on | Accept. |
| Only self-signed and/or hostname errors, with ACCEPT_INVALID_SSL_CERT off | Reject. |
| Any other error | Reject. |

The current compiled default of [ACCEPT_INVALID_SSL_CERT](../set/accept_invalid_ssl_cert.md) is on; users and scripts can change it. The setting's name does not mean that every category of invalid certificate is accepted.

### 6. Invoke SERVER_SSL_EVAL

On that normal policy path, EPIC gives scripts the provisional result:

| Argument | Meaning |
| --- | --- |
| `$0` | Server refnum. |
| `$1` | Configured server hostname, the name used in the server description. |
| `$2` | Any-verification-error flag. |
| `$3` | Hostname-error flag. |
| `$4` | Self-signed-error flag. |
| `$5` | Other-error flag. |
| `$6` | Provisional decision: `0` rejects, `1` accepts. |

The handler can inspect the following properties with `$serverctl(GET <refnum> <item>)`:

| Item | Meaning |
| --- | --- |
| `SSL_CIPHER` | Negotiated encryption cipher. |
| `SSL_PEM` | Certificate in PEM format. |
| `SSL_CERT_HASH` | Certificate hash. |
| `SSL_PKEY_BITS` | Public-key size in bits. |
| `SSL_SUBJECT` | Certificate subject. |
| `SSL_SUBJECT_URL` | URL-encoded subject. |
| `SSL_ISSUER` | Certificate issuer. |
| `SSL_ISSUER_URL` | URL-encoded issuer. |
| `SSL_VERSION` | Negotiated TLS protocol version. |
| `SSL_SANS` | Certificate subject alternative names. |
| `SSL_CHECKHOST_ERROR` | Hostname-error flag. |
| `SSL_SELF_SIGNED_ERROR` | Self-signed-error flag. |
| `SSL_OTHER_ERROR` | Other-error flag. |
| `SSL_MOST_SERIOUS_ERROR` | Representative OpenSSL verification error code, using the priority described above. |
| `SSL_VERIFY_ERROR` | Any-verification-error flag. |
| `SSL_ACCEPT_CERT` | Current decision value, initially `-1` before the early hook and normally `0` or `1` at this stage. |

To override the provisional decision inside SERVER_SSL_EVAL:

```text
$serverctl(SET <refnum> SSL_ACCEPT_CERT 0)
$serverctl(SET <refnum> SSL_ACCEPT_CERT 1)
```

The first rejects; the second accepts. Leaving it unchanged retains the provisional policy result.

### 7. Continue or reject

After SERVER_SSL_EVAL returns, the normal policy path rejects exactly `SSL_ACCEPT_CERT == 0`; otherwise it continues toward IRC registration. The early-hook bypass described above is why the original's unconditional claim that every final value is checked for acceptance or rejection needs qualification.

## September 2, 2021: Configuring SSL ciphers

```irc
/set ssl_ciphers <cipher-list>
```

The release added a cipher-list setting, unset by default so OpenSSL chooses its defaults. Current EPIC6 passes the setting to `SSL_CTX_set_cipher_list` when it first creates the shared TLS context. Set it before the first TLS connection: its current setting registration has no update callback, so later changes do not reconfigure the existing context. This API does not configure every TLS version's ciphersuites, and EPIC does not check its return value. See [ssl_ciphers](../set/ssl_ciphers.md).

## September 2, 2021: Earlier SSL handling announcement

The original entry was explicitly replaced by the September 6 second revision above.

## August 30, 2021: Removed ssl-strict field

The proposed server-description field `ssl-strict` was removed. This entry records that withdrawn announcement; it is not a supported current server-description field.

## Source validation

- Tag `epic5-2.1.6`, its `UPDATES` and `source/irc.c`, and release commit `1df4c951`: dated announcements and release identity; the tag also contains `script/reconnect.orig`.
- [source/server.c](../../source/server.c): PADDR, reconnection failure paths, SSL hook ordering, certificate-policy decisions, and SERVERCTL SSL getters/setter.
- [source/ssl.c](../../source/ssl.c), [source/vars.c](../../source/vars.c), and [include/config.h](../../include/config.h): verification categories, certificate-hook arguments/hash, SSL cipher configuration, setting registrations, and compiled acceptance default.
- [source/window.c](../../source/window.c), [source/lastlog.c](../../source/lastlog.c), and [source/levels.c](../../source/levels.c): CLEARREGEX/CLEARLEVEL registration, matching, history removal, and level names.
- [source/commands.c](../../source/commands.c), [source/alias.c](../../source/alias.c), and [source/names.c](../../source/names.c): argument-list statements, variable scope, and CHANNEL_LOST arguments.
- [source/functions.c](../../source/functions.c) and [source/hook.c](../../source/hook.c): JSON function implementations/registrations and event registrations.
- [script/reconnect](../../script/reconnect) and [script/reconnect.old](../../script/reconnect.old): current reconnect implementations and filenames.
