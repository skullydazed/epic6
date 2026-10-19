# set status_insert

## Synopsis

```text
/set status_insert [<text>]
/set -status_insert
```

Default: an empty string. With no text, [set](../commands/set.md) displays the current value. Unsetting also leaves the indicator without visible text.

## Description

Sets the status-bar string displayed when [insert_mode](insert_mode.md) is on for input editing. When insert mode is off, this field is empty; [status_overwrite](status_overwrite.md) supplies the separate overwrite-mode indicator.

The field is represented by `%I` in [status_format](status_format.md), or a per-window status format. The text is inserted directly: `%I` inside `status_insert` is literal rather than a nested substitution. The outer format must contain `%I` for the indicator to appear.

With [show_status_all](show_status_all.md) off, the field appears only in the input window of each screen. Turning that setting on permits it in other windows too, but it still reflects the same global insert-mode setting.

## Effects and expansion

Changing the text rebuilds status formats and requests updates. It does not enable insert mode, alter input text, or change key bindings. Use `set insert_mode` or the [toggle_insert_mode](../bind/toggle_insert_mode.md) binding to change the editing mode.

Ordinary command parsing can expand dollar expressions while assigning the value. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can also be expanded afterward. Status percent fields are not recursively interpreted in this marker.

## Example

Customize the insert-mode indicator:

```text
/set status_insert (Inserting)
```

It appears where `%I` is included in the status format while insert mode and the visibility conditions permit. Use `/set -status_insert` to hide this indicator without changing editing mode.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and empty default.
- [source/status.c](../../source/status.c): `%I` maps to `status_insert_mode`, which checks the global mode and current-window policy before returning the text.
