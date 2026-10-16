# window balance

## Synopsis

```text
/window [<window>] balance
```

## Description

Resize the non-fixed windows on the selected window's screen so their scrolling output areas are as close to equal in height as possible. With no window selector, use the current window's screen. Window order is preserved.

A window with [window fixed](fixed.md) on keeps its output height. The remaining windows divide their combined existing output rows equally; when there is a remainder, the first non-fixed windows in screen order receive one extra row each. Their output heights therefore differ by at most one row after balancing.

Balancing equalizes output rows, not total window height. Status bars and toplines retain their own row counts, so windows with different numbers of those lines can still occupy different total heights. Fixed windows can move vertically as neighboring windows change size, even though their own output heights stay fixed.

The built-in operation takes no arguments or options. Additional words are processed as subsequent window operations. It requests window-body and status-bar redraws, preserves stored output and input text, and sends no IRC request. It normally produces no text output.

## Edge cases

If every window on the screen is fixed, EPIC reports `All the windows on this screen are fixed!` and makes no size changes. Selecting an invisible window reports `Cannot balance invisible windows!`. `/window -balance` is a no-op, and the balancing helper does nothing outside fullscreen mode.

A single non-fixed window keeps the same number of output rows. Balancing does not impose a positive minimum on every non-fixed output area; if there are too few rows, some can have zero output rows.

## Examples

```text
/window balance
/window 3 balance
```

The first balances the current screen's windows; the second balances windows on the screen containing window 3. For example, non-fixed output areas of 10, 5, and 5 rows become 7, 7, and 6 rows in screen order.

Use [window grow](grow.md), [window shrink](shrink.md), or [window size](size.md) to adjust individual window sizes.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_balance`, `rebalance_windows`, `set_window_display_lines`, and `recalculate_window_positions` implement screen selection, row distribution, fixed-window exclusion, and redraw requests.
