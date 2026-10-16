# window scroll_start

## Synopsis

```text
/window [<window>] scroll_start
```

## Description

Move the selected window's scrollback view toward the oldest retained output. With no window selector, use the current window. In ordinary-sized buffers this reaches the top of scrollback in one operation.

The implementation requests **999999 display rows** of backward movement and stops at the oldest retained row. It is a large fixed step, not an unlimited jump; a larger buffer can require repeated calls. The amount does not depend on [set scrollback_ratio](../set/scrollback_ratio.md).

If already browsing scrollback, movement starts at that view. Otherwise it starts at the held view when that view is older than the normal scrolling view, or at the normal view otherwise. EPIC recalculates positions and requests window-body and status updates. It does not change [window hold_mode](hold_mode.md). See [window](../commands/window.md) for the different views.

While scrollback remains active, new output continues to be stored without normally scrolling the older view away. Automatic display-buffer trimming is suspended, allowing memory use to grow beyond [window scrollback](scrollback.md)'s configured size. This is not a guarantee against history loss: flushing, lastlog trimming, or [scrollback rebuilding](rebuild_scrollback.md) can still remove old output, and rebuilding is limited by retained lastlog data.

Use [window scroll_forward](scroll_forward.md) to read newer output or [window scroll_end](scroll_end.md) to advance toward the end. Normal trimming can resume after leaving scrollback when no held backlog prevents it.

## Output and arguments

If the scrollback view is already at its oldest row, another attempt requests a bell, subject to [set beep](../set/beep.md) and terminal support. Successful movement normally produces no confirmation message.

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations. `/window -scroll_start` is a no-op. The view of an invisible window can change without making it visible. Input selection is unchanged, and no IRC request is sent.

## Examples

```text
/window scroll_start
/window 3 scroll_start
```

Read the oldest retained output in the current window or window 3, subject to the fixed-step limit above.

## History

This operation first appeared in EPIC4-1.1.11.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_scroll_start`, `window_scrollback_start`, `window_scrollback_backwards_lines`, and `window_scrollback_backwards` implement the fixed step, starting views, and oldest-row limit; `trim_scrollback` suspends normal trimming while browsing.
- [source/term.c](../../source/term.c): `term_beep` applies beep settings to the oldest-row bell request.
