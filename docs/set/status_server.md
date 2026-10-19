# set status_server

## Synopsis

```text
/set status_server [<text>]
/set -status_server
```

Default: ` (%S)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the server-name wrapper rather than restoring the default.

## Description

Sets the format used by server-name fields in [status_format](status_format.md), or per-window status formats. The first `%S` inside `status_server` is replaced with the selected server value before insertion into the outer line. Other status percent fields are not recursively interpreted there.

The selected value depends on the outer field:

| Outer field | Selected value |
| --- | --- |
| `%S` | First configured server alias, falling back to the configured server name. Hidden when exactly one server is connected. |
| `%{1}S` | Same alias/name selection without the single-server hiding rule. |
| `%{2}S` | Full configured server name. |
| `%{3}S` | Server group, falling back to configured name when absent. |
| `%{4}S` | Server-reported name, falling back to configured name when absent. |

Use plain `%S` in this wrapper even when the outer format uses a mapped variant. The outer format must contain an applicable field. Fields reflect each window's associated server, not only the selected input window, and [show_status_all](show_status_all.md) does not change the single-server rule for plain `%S`.

## Fallbacks and related fields

For these fields, a window without an associated server supplies the literal `No Server`, bypassing the wrapper. Plain `%S` first applies its exactly-one-connected-server hiding rule, which can hide even that fallback. If no server name can be obtained for an associated server, the field supplies `Unknown`, also without the wrapper. Unsetting this setting therefore does not suppress those direct fallback messages.

`%{5}S` is a separate connection-state field and does not use this wrapper. `%G` similarly displays the server's advertised NETWORK name independently. Current server aliases are used as configured; the status handler does not shorten a hostname by selecting one dotted segment.

## Effects and example

Changing the text rebuilds status formats and requests updates. It does not connect, disconnect, change server names, or send an IRC request. Ordinary command parsing can expand dollar expressions on assignment. [Status_does_expandos](status_does_expandos.md) optionally expands the assembled line later; inserted server values are escaped for dollar signs and backslashes by the wrapper helper.

```text
/set status_server Server: %S
```

An applicable outer field can then supply `Server: example`. To show it even with one connected server, use `%{1}S` in the outer format. Use `/set status_server (%S)` for a parenthesized name without the default leading space.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, status rebuild callback, and default wrapper.
- [source/status.c](../../source/status.c): mapped server fields, `status_server` selection and direct fallbacks, separate state/network fields, and wrapper compilation.
- [source/server.c](../../source/server.c): aliases, configured and reported names, and server-group accessors.
