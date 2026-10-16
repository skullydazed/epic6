# window double

## Synopsis

```text
/window [<window>] double [on|off|toggle]
```

## Description

Choose whether the selected window uses one or two status lines. With no selector, operate on the current window. `on` uses two lines, `off` uses one, and `toggle` switches between them. Each window has its own setting; new windows start with one status line.

Two status lines can show more information at once. In single-line mode the window uses its `status_format`; in double-line mode it uses `status_format1` and `status_format2`. These formats normally come from the corresponding global settings, but can be overridden for an individual window with [window status_format](status_format.md), [window status_format1](status_format1.md), and [window status_format2](status_format2.md).

Changing the status-line count normally takes one row from, or returns one row to, the window's scrolling output area so its total height remains the same. Window positions and redraws are recalculated. If adding a status line would make the output height negative, EPIC sets the output height to zero and recalculates the visible screen layout as needed.

The built-in operation changes local layout, preserves stored output and input text, and sends no IRC request. It also works on an invisible window's stored layout without making that window visible.

## Output and edge cases

With no value, report the current state without changing it. Queries and successful changes print `Window DOUBLE is ON` or `Window DOUBLE is OFF`. Invalid values report `Value for DOUBLE must be ON, OFF, or TOGGLE` and leave the state unchanged.

`/window -double` is a no-op. There are no other operation-specific options; additional words are processed as subsequent window operations. Changing this setting does not remove or overwrite the saved status format for the other mode.

## Examples

```text
/window double on
/window 3 double toggle
/window 3 double
```

Enable two status lines in the current window, toggle window 3's setting, then inspect its state.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_double`, `get_boolean`, window initialization, and position recalculation implement the setting, reports, and output-height adjustment.
- [source/status.c](../../source/status.c): `compile_status` selects default formats, while `make_status` chooses the single or double format slots.
