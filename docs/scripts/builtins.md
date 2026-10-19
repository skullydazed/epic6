# builtins

## Synopsis

```text
/load builtins
```

## Description

This bundled script supplies compatibility commands, functions, and settings descended from EPIC4. Some approximate the older behavior rather than duplicate it. They are script definitions layered on current C commands and control functions; the script's name does not make them C built-ins.

The default `global` startup script loads `builtins`. A personal startup file replaces that default startup selection, so load it explicitly if your configuration needs these interfaces. Loading it installs aliases and hooks, loads supporting scripts, and initializes settings to the values below. Reloading can replace definitions and reset settings; it is not a read-only operation.

## Commands

| Script command | Behavior |
| --- | --- |
| `bye`, `exit`, `signoff` | Forward arguments to [quit](../commands/quit.md). |
| `date` | Forward to [time](../commands/time.md). |
| `host` | Forward to [userhost](../commands/userhost.md). |
| `irchost` | Forward to [hostname](../commands/hostname.md). |
| `ircname`, `realname` | Forward to the `set realname` compatibility hook, which assigns [default_realname](../set/default_realname.md). Use the canonical setting for queries and unsetting. |
| `ircuser` | Assign [default_username](../set/default_username.md). |
| `leave` | Forward to [part](../commands/part.md). |
| `whowas <nick> [<arguments>]` | Forward to built-in [whowas](../commands/whowas.md), defaulting omitted trailing arguments to `num_of_whowas` (`1`). |
| `dump`, `save` | Stubs that load the corresponding [dump](dump.md) or [save](save.md) script on demand. |

Forwarded network requests retain the underlying command's server requirements and server-dependent results. These aliases do not guarantee that a server supports or accepts a request. The script also loads [addset](addset.md), supplying `addset` and `delset` for script-defined settings.

## Encoding and hashing functions

These seven functions are script wrappers around the C [xform](../functions/xform.md) function. Each accepts the text inside its parentheses as a single input string, including internal spaces, and returns a string. They perform no file reading, network requests, or character-set conversion.

| Function syntax | Transform | Return value |
| --- | --- | --- |
| `$b64encode(<text>)` | `+b64` | Standard Base64 using `A–Z`, `a–z`, `0–9`, `+`, `/`, and `=` padding. |
| `$b64decode(<text>)` | `-b64` | Decoded bytes as an EPIC string. |
| `$encode(<text>)` | `+enc` | Two uppercase letters `A` through `P` per input byte, encoding its high and low hexadecimal nibbles. |
| `$decode(<text>)` | `-enc` | Reverse of that letter-pair encoding. |
| `$sha256(<text>)` | `+sha256` | SHA-256 digest as 64 uppercase hexadecimal digits. |
| `$urlencode(<text>)` | `+url` | Percent-encoded bytes, leaving only ASCII letters, digits, `-`, `.`, `_`, and `~` unchanged. |
| `$urldecode(<text>)` | `-url` | Decode valid `%hh` sequences; other characters pass through. |

Spaces encode as `%20`, not `+`; URL decoding leaves a literal `+` unchanged. This is component escaping, not whole-URL parsing. Encoding UTF-8 text escapes its individual bytes.

Empty input produces an empty result for the reversible transformations; `sha256()` returns the digest of the empty string. Decoders are not validation functions and do not return a separate success flag: Base64 decoding stops at an invalid or incomplete four-character group and returns the prefix decoded so far; it does not skip whitespace. `decode` ignores an unmatched final character and does not check that paired characters are in `A` through `P`. Invalid URL escapes are left unchanged. Decoded NUL bytes cannot be reliably carried through ordinary EPIC string use; these wrappers are intended for text rather than arbitrary binary round trips.

Examples:

```text
/echo $b64encode(hello)
/echo $b64decode(aGVsbG8=)
/echo $encode(A)
/echo $decode(EB)
/echo $urlencode(a b+c)
/echo $urldecode(a%20b%2Bc)
/echo $sha256(abc)
```

The first six results are `aGVsbG8=`, `hello`, `EB`, `A`, `a%20b%2Bc`, and `a b+c`. SHA-256 uses the OpenSSL implementation required by the current build; there is no separate script switch to enable it.

## Other compatibility functions

Server wrappers accept an optional server reference, normally defaulting to the current server context:

| Function | Script behavior |
| --- | --- |
| `lastserver()` | Return `serverctl(last_server)`. |
| `myservers([<flag>])` | List connected servers' reported names, or their refnums when a nonempty argument is supplied. |
| `servergroup([<server>])` | Return the configured group, falling back to `<default>`. |
| `servername([<server>])` | Return the server's reported name, falling back to `<none>`. |
| `serverourname([<server>])` | Return the configured server name, falling back to `<none>`. |
| `servernick([<server>])` | Return the stored nickname. |
| `servernum([<server>])` | Resolve the reference, returning `-1` on failure. |
| `servertype([<server>])` | Return the configured protocol. |
| `servports([<server>])` | Return remote and local ports as two words. |
| `notifywindows()` | List windows with their notification flag set. |

Window wrappers generally accept an optional window selector, defaulting to `0` (the current window), and query `windowctl`: `wincursorline`, `winlevel`, `winnam`, `winnicklist`, `winnum`, `winquery`, `winscreen`, `winscrollbacksize`, `winserv`, `winsize`, `winstatsize`, and `winvisible`. `winrefs()` lists window refnums. Results and invalid-selector handling depend on the requested field; several wrappers return `-1` when lookup fails. `winstatsize` adds one to the stored double-status flag. `winbound()` always returns an empty string.

The retained `winline` alias contains a malformed nested `windowctl` lookup; do not rely on it for retrieving display lines. Likewise, `igmask`, `igtype`, `rigmask`, and `rigtype` still call the removed `ignorectl` interface and do not restore the old ignore system. `clr_256(<r> <g> <b>)` returns `16 + r*36 + g*6 + b`; it expects color-cube components from 0 through 5 but does not validate them or emit a color escape.

## Settings and hooks

The following settings are supplied or initialized by this script:

| Setting | Initial value | Effect |
| --- | --- | --- |
| `auto_rejoin` | `off` | On a kick targeting your nickname, request rejoining the channel when enabled. |
| `auto_rejoin_delay` | `0` | Delay that request in seconds; zero uses a deferred join. Server restrictions still apply. |
| `auto_unmark_away` | `off` | Clear away status on ordinary input, and on selected talking commands (`/me`, `/msg`, `/notice`, `/say`). |
| `auto_whowas` | `on` | Issue `whowas` for a nonchannel target reported in numeric 401. |
| `beep_on_msg` | `none` | Request a beep for selected event categories, or `all`; it does not require away status. |
| `full_status_line` | `on` | Adjust trailing reset controls in the three global status format settings. |
| `num_of_whowas` | `1` | Default count argument for the script's `whowas` alias. |
| [show_end_of_msgs](../set/show_end_of_msgs.md) | `on` | Permit selected end-of-list numerics; off suppresses them through hooks. |
| [show_who_hopcount](../set/show_who_hopcount.md) | `on` | Current enabled hook prints a replacement WHO row that omits the hop count, despite the setting's name. |
| [verbose_ctcp](../set/verbose_ctcp.md) | `on` | Permit ordinary CTCP request display; off suppresses that display, not handlers or replies. |

`beep_on_msg` recognizes `action`, `crap`, `ctcp`, `msgs`, `notices`, `opnotes`, `public`, `snotes`, and `wallops`. Categories are selected by word-list membership; not every event at a similarly named output level has a matching hook. Actual sound also depends on the current beep implementation and terminal.

The script loads [newnick](newnick.md) for nickname-conflict handling, [ctcp](ctcp.md) for CTCP responders, [mail](mail.md) for Python-based mail support, and [notify](notify.md) for server MONITOR support. Their behavior and requirements belong to those scripts; loading `builtins` does not make Python mail checking or server MONITOR universally available. It also installs a replacement `send_public` display hook.

The `command_mode` block is disabled and creates no setting or input hooks. Retained DCC beep hooks and `dcc_timeout` code refer to removed DCC interfaces; setting their values does not restore DCC transfers or idle-transfer closure. Compatibility definitions and stale code in this file should not be treated as proof that their former subsystems still exist.

## History

This script was first included in EPIC5 to provide compatibility with EPIC4.

## Source references

- [script/builtins](../../script/builtins): command/function aliases, initialized settings, hooks, and supporting loads.
- [script/global](../../script/global) and [source/irc.c](../../source/irc.c): default versus personal startup selection.
- [source/functions.c](../../source/functions.c): function dispatch and `xform` string handling.
- [source/ircaux.c](../../source/ircaux.c): Base64, letter-pair, URL, and OpenSSL SHA-256 transforms, including decoder edge cases.
- [source/commands.c](../../source/commands.c), [source/server.c](../../source/server.c), and [source/window.c](../../source/window.c): underlying commands and control interfaces.
- [configure.ac](../../configure.ac): required OpenSSL support.
