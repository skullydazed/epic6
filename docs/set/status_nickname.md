# set status_nickname

## Synopsis

```text
/set status_nickname [<text>]
/set -status_nickname
```

Default: `%N`, displaying the nickname alone. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the nickname field's format rather than restoring the default.

## Description

Determines how the `%N` field in [status_format](status_format.md) displays the nickname used on that window's server. The first `%N` inside `status_nickname` is replaced with the nickname before the result is inserted into the outer status line. Other status percent fields are not recursively expanded there.

The outer global or per-window format must contain `%N`. Omitting `%N` from this wrapper displays fixed wrapper text instead of a nickname. The setting's full name is `status_nickname`; `status_nick` is accepted as its unambiguous prefix when no conflicting setting is present.

The nickname comes from the window's associated server, not necessarily the selected input window's server. This field has no current-window restriction under [show_status_all](show_status_all.md). It can also show the server getter's placeholder when no valid server or registered nickname is available.

## Effects and expansion

Changing this format rebuilds status formats and requests updates. It does not change your nickname or send an IRC request; use [nick](../commands/nick.md) for that. Server acceptance and later nickname changes determine the actual nickname shown.

Ordinary command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, the assembled status line can expand dollar expressions afterward. The shared wrapper helper escapes dollar signs and backslashes in the inserted nickname for that later pass.

## Example

Customize the nickname display:

```text
/set status_nickname [You Are %N]
```

For nickname `alice`, this supplies `[You Are alice]` where `%N` appears in the outer format. Restore the simple display with `/set status_nickname %N`, or hide it with `/set -status_nickname`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, prefix lookup, and `%N` default.
- [source/status.c](../../source/status.c): `%N` mapping, wrapper compilation, `status_nickname`, and shared insertion escaping.
- [source/server.c](../../source/server.c): `get_server_nickname` supplies the per-server nickname or fallback text.
