# set status_oper

## Synopsis

```text
/set status_oper [<text>]
/set -status_oper
```

Default: `*`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the operator marker rather than restoring the default.

## Description

Sets the status-bar string displayed when EPIC records IRC-operator status on the window's server. If the window has no server or EPIC does not record operator status there, the field is empty. This represents server-level IRC operator status, not channel-operator privileges; [status_chanop](status_chanop.md) is separate.

The field is represented by `%*` in [status_format](status_format.md), or a per-window format. The marker is inserted directly: `%*` inside `status_oper` is literal rather than a nested substitution. The outer format must contain `%*` for the marker to appear.

When exactly one server is connected and [show_status_all](show_status_all.md) is off, the field appears only in the input window of each screen. With multiple connected servers, that restriction does not apply; each window checks its own server. Enabling `show_status_all` also permits the marker in noncurrent windows when only one server is connected.

## Effects and expansion

Changing the marker rebuilds status formats and requests updates. It does not authenticate as an operator, change user modes, or send an IRC request. Operator privileges and how they are granted are server-dependent; the display reflects the client's recorded user-mode state.

Ordinary command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can also expand afterward.

## Example

Restore the default operator marker:

```text
/set status_oper *
```

Use `/set -status_oper` to hide it without changing operator status or privileges.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and `*` default.
- [source/status.c](../../source/status.c): `%*` mapping and `status_oper` server, operator, and visibility tests.
- [source/server.c](../../source/server.c): `get_server_operator` derives operator state from the recorded user modes.
