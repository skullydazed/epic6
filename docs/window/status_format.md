# window status_format

## Synopsis

```text
/window [<window>] status_format ["<text>"]
```

## Description

Override the selected window's single-row status-bar format. With no window selector, use the current window. Normally, a window inherits [set status_format](../set/status_format.md); this operation supplies a format for just one window, leaving the global setting and other windows' overrides unchanged.

The format uses the same status expandos and formatting rules as `set status_format`, including any applicable [set status_does_expandos](../set/status_does_expandos.md) processing. Custom formats still obey [set show_status_all](../set/show_status_all.md): expandos restricted to the screen's current input window do not automatically appear in every window merely because the format is custom.

This value is displayed only when [window double](double.md) is off. With double status enabled, [window status_format1](status_format1.md) and [window status_format2](status_format2.md), or their global defaults, supply the two rows instead. Setting `status_format` while double status is on still stores it for later single-row use.

## Setting and removing the override

The text is one argument. Put it in double quotes so spaces are included. An empty quoted string stores an explicitly empty format; it does not restore the global default or remove the status row itself.

With no argument, remove the custom format and return to the global single-row format. This is **not a query**. In contrast, `/window -status_format` is a no-op and does not remove the override. To remove it, place ordinary `status_format` at the end of the command, with nothing after it.

The operation recompiles the window's status formats and requests a status redraw. Successful changes and removal normally produce no confirmation message. It works on invisible windows without making them visible, does not change the number of status rows or input selection, and sends no IRC request.

Additional words after a supplied text argument are processed as subsequent window operations. A following operation without a text argument would instead be consumed as the new format, so use separate commands when restoring inheritance.

## Examples

```text
/window 3 status_format "Window %R: %N"
/window 3 status_format ""
/window 3 status_format
```

Give window 3 a custom format containing its name/refnum and nickname, use an empty format, or remove its override and inherit the global setting again. These formats are visible when window 3 uses single-row status.

## History

This operation first appeared in EPIC4pre2.001.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_status_format` stores the override or clears it, calls `compile_status`, and requests redraw; `windowcmd_double` selects one or two status rows.
- [source/status.c](../../source/status.c): `compile_status` chooses per-window overrides or global defaults; `make_status` selects slot 0 for single-row status and slots 1/2 for double status. `DISPLAY_ON_WINDOW` enforces current-window expando rules.
