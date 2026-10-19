# set status_halfop

## Synopsis

```text
/set status_halfop [<text>]
/set -status_halfop
```

Default: `%`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the half-operator marker text rather than restoring the default.

## Description

Sets the status-bar string for being a half-operator on a window's current channel. Half-operator privileges are server-dependent; some servers call the role a channel helper. The indicator uses EPIC's cached channel state and does not grant or remove privileges.

This text shares the `%@` field in [status_format](status_format.md) with [status_chanop](status_chanop.md). EPIC checks the operator marker first: if you are an operator and `status_chanop` is set, that text takes precedence. Otherwise, if EPIC records you as a half-operator and `status_halfop` is set, this text is used. If neither branch supplies text, or the window has no server or current channel, the field is empty.

The text is inserted directly. `%@` has no special meaning inside `status_halfop`, and the default single `%` need not be doubled in this setting. The outer global or per-window status format must contain `%@` for either marker to appear.

## Effects and expansion

The field is not limited to the selected input window by [show_status_all](show_status_all.md); each window uses its own current channel. Changing the setting rebuilds status formats and requests updates without sending an IRC request or refreshing privilege information from the server.

Ordinary command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can also be expanded afterward. That is separate from the direct marker insertion.

## Example

Restore the default half-operator indicator:

```text
/set status_halfop %
```

Use `/set -status_halfop` to remove this marker text. An operator marker can still appear through the same `%@` field when its branch applies.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and `%` default.
- [source/status.c](../../source/status.c): `%@` maps to `status_chanop`, which checks operator text before the half-operator fallback.
- [source/names.c](../../source/names.c): `get_channel_halfop` returns cached half-operator state.
