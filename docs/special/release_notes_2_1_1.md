# EPIC5 2.1.1 release notes

Released **February 24, 2019**, release name **Abulia**. Repository tag `epic5-2.1.1` identifies commit ID **1898**, correcting 1899 in the original release-note heading. Its Git release commit is `1b673a0d`.

These are historical EPIC5 announcements. The current EPIC6 implementation retains much of the CTCP and status-prefix machinery, but the compatibility notes below distinguish current behavior from the original release claims. See the [release index](release_notes.md).

## February 5, 2018: CTCP UTC moved to script

The release announced that CTCP PING's response handling had been rewritten and CTCP UTC moved to script, using the distinction between argument replacement and whole-CTCP replacement described next. This date and ordering are retained from the tagged `UPDATES` entry.

## February 13, 2018: REPLACE_ARGS for CTCP handlers

A CTCP handler returning text can replace either its argument text or the CTCP representation in the containing message.

For PING, the original announcement illustrated argument replacement schematically:

```text
NOTICE nick :\001PING <seconds> <microseconds>\001
```

becoming:

```text
NOTICE nick :\001PING <elapsed> seconds\001
```

UTC instead replaces its CTCP representation with a local date/time string:

```text
PRIVMSG nick :\001UTC 1518582810\001
```

becoming locally formatted text such as:

```text
PRIVMSG nick :Tue Feb 13 22:33:30 2018
```

Here `\001` denotes a CTCP delimiter, and the date/time is illustrative: the result depends on the receiving host's timezone and locale. These describe local parsing substitutions, not additional messages transmitted to the server.

```text
$ctcpctl(SET <ctcp-name> REPLACE_ARGS 1)
$ctcpctl(SET <ctcp-name> REPLACE_ARGS 0)
```

`1` selects argument replacement; `0`, the default for a newly registered ordinary CTCP, selects whole-CTCP replacement. The current parser replaces the text before and including the CTCP when performing that whole replacement, then appends the suffix. A leading text label can therefore be lost; see [UTC](../ctcp/utc.md).

## February 5, 2018: Core CTCP handlers became scripts

The release moved VERSION, PING, ECHO, CLIENTINFO, USERINFO, ERRMSG, FINGER, and TIME handling into `script/ctcp`, giving users control without changing C code. The startup instruction was:

```irc
/load ctcp
```

`builtins` loads that script, and the default `global` startup loads `builtins`. Personal startup configurations that omit those scripts need to arrange the desired definitions explicitly. The current EPIC6 supplied scripts still use this arrangement, with UTC implemented in the same file.

## February 4, 2018: User-defined CTCP handlers

A CTCP callback receives:

| Argument | Meaning |
| --- | --- |
| `$0` | Sender of the request or reply. |
| `$1` | Destination, such as your nickname or a channel. |
| `$2` | CTCP name. |
| `$3-` | Remaining CTCP argument text. |

Request callbacks process incoming PRIVMSG CTCPs. They can issue a response or return replacement text. Inside a request callback, a response can be sent with:

```irc
ctcp $0 $2 Your response here
```

The CTCP command chooses NOTICE framing in this incoming-private-message context. Response callbacks process incoming NOTICE CTCPs and can interpret them or return replacement text. Ordinary reply display remains available, so a custom response handler is often unnecessary. Returning text alone does not universally suppress generic CTCP diagnostics; the replacement and SPECIAL flags control distinct parts of processing.

The interfaces introduced were:

```text
$ctcpctl(SET <ctcp-name> REQUEST {<code>})
$ctcpctl(SET <ctcp-name> RESPONSE {<code>})
$ctcpctl(SET <ctcp-name> DESCRIPTION <text>)
$ctcpctl(SET <ctcp-name> SPECIAL 1)
$ctcpctl(SET <ctcp-name> SPECIAL 0)
$ctcpctl(SET <ctcp-name> RAW 1)
$ctcpctl(SET <ctcp-name> RAW 0)
$ctcpctl(ALL)
```

REQUEST and RESPONSE install script callbacks. DESCRIPTION supplies the description used by the scripted CLIENTINFO responder. ALL lists registered names.

SPECIAL marks a handler as self-contained: after its handler runs, generic CTCP handling is skipped. The original release named ACTION and DCC as the special built-ins of that period. EPIC6 has removed DCC; its current initialization registers ACTION, with other handlers supplied by scripts or later registrations. “Remote function call” in the original was a conceptual description of handler dispatch, not permission for a sender to execute arbitrary local code.

RAW lets a handler receive the original CTCP argument representation without ordinary text recoding. Binary-oriented CTCP payloads historically used CTCP encoding, and `$xform(-CTCP <data>)` can decode that representation. Decoded data can contain NUL bytes and therefore cannot necessarily be stored safely as an ordinary script string. Raw handling and text encoding are distinct concerns.

The current getter supports REQUEST, RESPONSE, DESCRIPTION, SPECIAL, RAW, and ACTIVE for registered names. It does not implement `GET ... REPLACE_ARGS`. Query only names known to be registered: the current C GET path does not guard a failed lookup before accessing the entry. See [`ctcpctl`](../functions/ctcpctl.md) for its current restrictions.

## January 16, 2018: Status prefix expando %{1}P

`%{1}P` selects a prefix according to whether the window is current. A window-specific value takes precedence over its corresponding global setting:

| Window state | Per-window operation | Global fallback |
| --- | --- | --- |
| Current | `window status_prefix_when_current` | `set status_prefix_when_current` |
| Not current | `window status_prefix_when_not_current` | `set status_prefix_when_not_current` |

The original theme example was:

```text
set status_format %{1}P%T [%R] %*%=%@%N%#%S%{1}H%H%B%Q%A%C%+%I%O%M%F%L %D %U %W
set status_prefix_when_current ^C37,40
set status_prefix_when_not_current ^C37,44
```

It was intended to distinguish current and non-current status bars with colors. In this printed example, `^C` represents the color control character; the two visible characters are not a guarantee of color interpretation. This entire historical format should not be copied as an EPIC6 feature list: it includes status fields for retired facilities. The current prefix lookup itself remains implemented; see [status prefix settings](../set/status_prefix_when_current.md) and [per-window prefix](../window/status_prefix_when_current.md).

## Source validation

- Tag `epic5-2.1.1`, `source/irc.c` and `UPDATES`: release identity and all dated announcements above.
- [source/ctcp.c](../../source/ctcp.c): callback arguments, flags, substitution, registration, and GET limitations.
- [script/ctcp](../../script/ctcp), [script/builtins](../../script/builtins), and [script/global](../../script/global): supplied handlers and loading chain.
- [source/status.c](../../source/status.c), [source/vars.c](../../source/vars.c), and [source/window.c](../../source/window.c): `%{1}P`, prefix precedence, and registered settings/operations.
