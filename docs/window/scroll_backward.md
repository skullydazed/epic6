# window scroll_backward

## Synopsis

```text
/window [<window>] scroll_backward [<rows>]
```

## Description

Move the selected window's scrollback view backward toward older output. With no window selector, use the current window. With no row count, or with zero, use [set scrollback_ratio](../set/scrollback_ratio.md) as a percentage of the window's output height. Its compiled default is 50. The ratio is limited to 1 through 100 for this calculation, the result is rounded down, and the minimum movement is one display row.

A positive `<rows>` requests that many wrapped display rows instead. Movement stops at the oldest retained row if less history is available. If the scrollback view is already at the oldest row, another attempt requests a bell, subject to [set beep](../set/beep.md) and terminal support.

If already browsing scrollback, movement starts at that view. Otherwise it starts at the held view when that view is farther back than the normal scrolling view, or at the normal view otherwise. The resulting scrollback view is selected for display, and EPIC requests body and status updates. It does not change the hold-mode setting. See [window](../commands/window.md) for the different views.

While scrollback is active, new output continues to be stored and normally does not scroll the older view away. Automatic display-buffer trimming is suspended, so the buffer can exceed [window scrollback](scrollback.md)'s configured size and use more memory. This does not guarantee indefinite retention through explicit flushing, lastlog trimming, or [scrollback rebuilding](rebuild_scrollback.md); rebuilding is limited by retained lastlog data.

Use [window scroll_forward](scroll_forward.md) to move toward newer output or [window scroll_end](scroll_end.md) to leave scrollback. Normal trimming can resume after leaving scrollback when no held backlog prevents it.

## Arguments and limits

If any words follow `scroll_backward`, the first is consumed as the row argument. A nonnumeric word reports `SCROLL_BACKWARD: You must specify a number`; use an explicit `0` before a following window operation to request default movement. Negative integers are accepted but do not reverse direction: they request no backward steps, although view bookkeeping can still occur. Use positive rows for explicit movement.

Additional words after the count are processed as subsequent window operations. `/window -scroll_backward` is a no-op. An invisible window's view can be changed without making it visible; there is no immediate physical display change in that case. The operation does not change input selection or send an IRC request, and normally prints no confirmation message.

## Examples

```text
/window scroll_backward
/window 3 scroll_backward 10
/window scroll_backward 0 refresh
```

Move back by the configured percentage, move window 3 back ten display rows, or use the default amount and then process pending updates.

## History

This operation first appeared in EPIC4-1.1.11.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_scroll_backward`, `window_scrollback_backward`, `window_scrollback_backwards_lines`, `window_scroll_lines_tester`, and `window_scrollback_backwards` implement argument handling, percentage movement, starting views, and limits. `trim_scrollback` suspends normal trimming while browsing.
- [include/config.h](../../include/config.h): defines the compiled scrollback ratio.
- [source/term.c](../../source/term.c): `term_beep` handles the oldest-row bell request.
