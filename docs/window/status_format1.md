# window status_format1

## Synopsis

```text
/window [<window>] status_format1 ["<text>"]
```

## Description

Override the selected window's top row of double status. With no window selector, use the current window. Normally that row inherits [set status_format1](../set/status_format1.md). Changing this override does not change the global format, another window's override, or the lower row supplied by [window status_format2](status_format2.md).

The value is used only when [window double](double.md) is on. Setting it while double status is off still saves it for later use, but single-row status continues using [window status_format](status_format.md) or its global default. This operation does not enable double status or change the number of status rows.

Use the status expandos and formatting rules described in [set status_format](../set/status_format.md). Custom formats still obey [set show_status_all](../set/show_status_all.md): expandos restricted to a screen's current input window do not automatically appear everywhere. [set status_does_expandos](../set/status_does_expandos.md) also applies normally.

## Setting and clearing

The text is one argument; use double quotes for spaces. An empty quoted string stores an explicitly empty format. It does not remove the override, restore inheritance, or remove the status row.

With no argument, clear the custom top-row format and return to the global `status_format1`. This is **not a query**. `/window -status_format1` is a no-op instead. Place ordinary `status_format1` at the end of a command to clear it, because a following word would be consumed as the new format.

The operation recompiles the window's status formats and requests a redraw. It normally produces no confirmation message, works on invisible windows without showing them, leaves input selection unchanged, and sends no IRC request. Additional words after a supplied format are processed as subsequent window operations.

## Examples

```text
/window 3 double on status_format1 "Window %R: %N"
/window 3 status_format1 ""
/window 3 status_format1
```

Enable double status and customize its top row in window 3, use an explicitly empty top-row format, or clear that override and inherit the global format again.

## History

This operation first appeared in EPIC4pre2.001.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_status_format1` stores or clears slot 1, calls `compile_status`, and requests redraw; `windowcmd_double` chooses one or two rows.
- [source/status.c](../../source/status.c): `compile_status` chooses custom formats or global defaults; `make_status` and `redraw_status` use slot 1 for the upper double-status row, slot 2 for the lower row, and slot 0 for single status.
