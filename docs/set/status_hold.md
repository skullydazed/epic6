# set status_hold

## Synopsis

```text
/set status_hold [<text>]
/set -status_hold
```

Default: ` Held: `, including leading and trailing spaces. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the indicator text rather than restoring the default.

## Description

Sets the string displayed for `%H` in [status_format](status_format.md) when the window has output beyond its visible held or scrollback view. Specifically, it appears when either the holding distance or the scrollback distance exceeds that window's display height. This can happen while browsing scrollback even when hold mode is off.

The text is inserted directly. `%H` has no substitution meaning inside `status_hold`, and it does not automatically include a line count. The outer global or per-window status format must contain `%H` for the indicator to appear. Each window uses its own state, without the current-window restriction of [show_status_all](show_status_all.md).

The separate `%B` field uses [status_hold_lines](status_hold_lines.md) for a count rounded down to the window's hold interval. Both fields can be relevant to the same backlog, but `%B` can remain empty until the rounded count is positive. The separate `%{1}H` field uses [status_holdmode](status_holdmode.md) for enabled hold mode while its view has not filled; it does not use this setting.

## Effects and expansion

Changing the text rebuilds status formats and requests updates. It does not enable hold mode, release output, move through scrollback, or discard queued text. Use the window's hold and scrollback controls for those actions.

Ordinary command parsing can expand dollar expressions on assignment. [Status_does_expandos](status_does_expandos.md), if enabled, can additionally expand dollar expressions in the assembled status line. That is separate from directly inserting this marker.

## Example

Customize the backlog label:

```text
/set status_hold Held Lines: 
```

A format containing `%H%B` can place the label before the rounded count. Hiding the label with `/set -status_hold` does not hide `%B` or release held output.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default label.
- [source/status.c](../../source/status.c): `%H` mapping, distance tests in `status_hold`, separate `status_hold_lines` rounding, and `%{1}H` hold-mode handling.
- [source/window.c](../../source/window.c): window display height, holding distance, and scrollback state.
