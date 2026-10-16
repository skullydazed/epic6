# window grow

## Synopsis

```text
/window [<window>] grow <lines>
```

## Description

Increase the selected window's scrolling output area by `<lines>` rows. With no window selector, use the current window. Another visible window on the same screen loses the same number of rows to make room; the terminal itself does not grow. Status-bar and topline row counts are unchanged.

For a positive increase, EPIC searches downward through the other visible windows, wrapping from the bottom to the top, for the first window whose output area has at least the requested number of rows. It takes all the needed rows from that one window, rather than combining space from several windows. The donor can be reduced to zero output rows.

A window with [window fixed](fixed.md) on can be explicitly grown. In the current implementation the donor search can also choose a fixed window when it has enough rows, so fixed status does not reliably protect other windows from this operation.

Window order, input selection, and stored output are preserved. The changed geometry is redrawn. [window scrolladj](scrolladj.md) controls whether growth normally reveals earlier retained output in the added space; the saved clear boundary can limit that adjustment. No IRC request is sent, and successful execution normally produces no confirmation message.

## Arguments and limits

`<lines>` is required and must be an integer. Missing or nonnumeric input reports `GROW: You must specify a number`. Use a positive value to grow. Zero makes no size change, although the helper still requires another visible window. Negative values are accepted and attempt to shrink the target while enlarging another window; use [window shrink](shrink.md) for that purpose.

An invisible target reports `You cannot change the size of invisible windows!`. If the target is the only visible window, or no single other window can supply the requested positive increase, EPIC reports `Can't change the size of this window!`. An attempted resize that would leave either output area negative reports `Not enough room to resize this window!`. These failures leave the sizes unchanged.

There are no additional options. Words after the number are processed as subsequent window operations, including after a resize error. `/window -grow` is a no-op. Use [window size](size.md) to request an absolute output height, or [window balance](balance.md) to equalize non-fixed windows.

## Examples

```text
/window grow 3
/window 7 grow 2
```

Add three output rows to the current window, or two to window 7, provided another visible window on its screen can supply them.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_grow`, `get_number`, and `resize_window` implement argument parsing, donor selection, and row transfer; `set_window_display_lines`, `recalculate_windows`, `recalculate_window_positions`, and `resize_window_display` handle geometry and display adjustment.
