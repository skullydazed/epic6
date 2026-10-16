# window status_format2

## Synopsis

```text
/window [<window>] status_format2 ["<text>"]
```

## Description

Override the selected window's bottom row of double status. With no window selector, use the current window. Normally that row inherits [set status_format2](../set/status_format2.md). Changing this override does not change the global format, another window's override, or the top row supplied by [window status_format1](status_format1.md).

The value is displayed only when [window double](double.md) is on. Setting it while double status is off saves it for later use, but single-row status continues using [window status_format](status_format.md) or its global default. This operation does not enable double status or change the number of status rows.

Use the status expandos and formatting rules described in [set status_format](../set/status_format.md). Custom formats still obey [set show_status_all](../set/show_status_all.md): expandos restricted to a screen's current input window do not automatically appear in every window. [set status_does_expandos](../set/status_does_expandos.md) also applies normally.

## Setting and clearing

The format is one argument; use double quotes for spaces. An empty quoted string stores an explicitly empty format rather than restoring inheritance or removing the status row.

With no argument, clear the custom bottom-row format and return to the global `status_format2`. This is **not a query**. `/window -status_format2` is a no-op instead. Place ordinary `status_format2` at the end of a command to clear it, because a following word would be consumed as the new format.

The operation recompiles the window's status formats and requests a redraw. It normally produces no confirmation message, works on invisible windows without showing them, leaves input selection unchanged, and sends no IRC request. Additional words after a supplied format are processed as subsequent window operations.

## Examples

```text
/window 3 double on status_format2 "Window %R: %N"
/window 3 status_format2 ""
/window 3 status_format2
```

Enable double status and customize its bottom row in window 3, use an explicitly empty bottom-row format, or clear its override and inherit the global format again.

## History

This operation first appeared in EPIC4pre2.001.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_status_format2` stores or clears slot 2, calls `compile_status`, and requests redraw; `windowcmd_double` selects one or two rows.
- [source/status.c](../../source/status.c): `compile_status` chooses custom formats or global defaults; `make_status` and `redraw_status` use slot 2 for the lower double-status row, slot 1 for the upper row, and slot 0 for single status.
