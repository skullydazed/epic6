# set status_hold_lines

## Synopsis

```text
/set status_hold_lines [<text>]
/set -status_hold_lines
```

Default: `%B`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the count field's format rather than restoring the default.

## Description

Sets the format of the held-output count displayed for `%B` in [status_format](status_format.md). The first `%B` inside `status_hold_lines` is replaced with the rounded count before insertion into the outer status line. Other status percent fields are not recursively expanded there.

EPIC takes the greater of the window's holding and scrollback distances, subtracts its display height, and rounds the positive result down to a multiple of that window's [hold_interval](../window/hold_interval.md). A zero interval is treated as one for this calculation. If the backlog or rounded count is zero, the field is empty. Counts are display rows, including wrapping, rather than necessarily whole messages.

For example, with an interval of 10, a backlog of 9 rows supplies no field, 19 supplies `10`, and 20 supplies `20`. Multiples of ten are not mandatory: the window's interval determines the rounding. The count can appear while browsing scrollback even with hold mode off.

## Related fields and effects

The outer global or per-window format must contain `%B`. Each window uses its own state; the field is not restricted to the selected input window by [show_status_all](show_status_all.md).

[Status_hold](status_hold.md), represented by `%H`, supplies a separate backlog label. It can appear before this count becomes large enough to display, and clearing either setting does not clear the other. [Status_holdmode](status_holdmode.md) represents a different hold-mode condition.

Changing this text rebuilds status formats and requests updates. It does not change the count, interval, hold mode, or queued text, and does not release output. Ordinary command parsing can expand dollar expressions on assignment; [status_does_expandos](status_does_expandos.md) can additionally expand dollar expressions in the assembled status line.

## Example

Display a self-contained count:

```text
/set status_hold_lines (Held: %B)
```

An outer `%B` field then supplies, for example, `(Held: 20)` when the rounded backlog is 20. Use `/set status_hold_lines %B` to restore the default format, or `/set -status_hold_lines` to hide this field without discarding held output.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and `%B` default.
- [source/status.c](../../source/status.c): `%B` mapping, wrapper compilation, and `status_hold_lines` distance selection, rounding, and empty-field conditions.
- [source/window.c](../../source/window.c): per-window holding, scrollback, display-height, and hold-interval values.
