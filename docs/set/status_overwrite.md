# set status_overwrite

## Synopsis

```text
/set status_overwrite [<text>]
/set -status_overwrite
```

Default: ` (Overwrite)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the indicator rather than restoring the default.

## Description

Sets the status-bar string displayed when the input line is in overwrite mode, meaning [insert_mode](insert_mode.md) is off. When insert mode is on, this field is empty; [status_insert](status_insert.md) supplies the separate insert-mode indicator.

The field is represented by `%O` in [status_format](status_format.md), or a per-window format. The text is inserted directly: `%O` inside `status_overwrite` is literal, not a nested substitution. The outer format must contain `%O` for the marker to appear.

With [show_status_all](show_status_all.md) off, the field appears only in the input window of each screen. Turning that setting on permits it in other windows too. All instances reflect the same global editing-mode setting.

## Effects and expansion

Changing the text rebuilds status formats and requests updates. It does not enable overwrite mode, modify input text, or change key bindings. Use `set insert_mode off` or the [toggle_insert_mode](../bind/toggle_insert_mode.md) binding to change the editing mode.

Ordinary command parsing can expand dollar expressions while assigning the text. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can additionally expand afterward. Status percent fields are not recursively interpreted in this marker.

## Example

Customize the overwrite indicator:

```text
/set status_overwrite (Overwrite)
```

This omits the default leading space; include a separator in the outer format if desired. Hide the marker with `/set -status_overwrite` without changing the editing mode.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default text.
- [source/status.c](../../source/status.c): `%O` maps to `status_overwrite_mode`, which checks that insert mode is off and the window meets the visibility policy.
