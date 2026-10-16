# window scroll_forward

## Synopsis

```text
/window [<window>] scroll_forward [<rows>]
```

## Description

Move the selected window's scrollback view forward toward newer output. With no window selector, use the current window. With no row count, or with zero, move by [set scrollback_ratio](../set/scrollback_ratio.md) percent of the window's output height. Its compiled default is 50. The calculation limits the ratio to 1 through 100, rounds down to whole display rows, and uses at least one row.

A positive `<rows>` requests that many wrapped display rows instead. Movement stops at the end of available output. After moving, EPIC automatically turns off the scrollback view when it catches up to either the normal view or the held view. See [window](../commands/window.md) for the different views.

If no scrollback view is active but the held view is older than the normal scrolling view, this operation advances the held view instead. It can therefore reveal held output without disabling [window hold_mode](hold_mode.md). When scrollback is active, only that view is advanced in this call; catching up does not also advance an older held position. This movement uses the row count or scrollback ratio, not [window hold_slider](hold_slider.md).

The operation recalculates view positions and requests window-body and status updates. It does not discard the rows it advances over. Once scrollback ends and no held backlog prevents it, normal buffer trimming can resume. Use [window scroll_end](scroll_end.md) for a large step toward the end, or [window scroll_backward](scroll_backward.md) to read older output.

## Arguments and limits

If any words follow `scroll_forward`, the first is consumed as the row count. A nonnumeric argument reports `SCROLL_FORWARD: You must specify a number`. Use an explicit `0` before a subsequent window operation when requesting default movement. Negative integers are accepted but request no forward steps; they do not scroll backward, although view bookkeeping can still occur.

If neither scrollback nor an older held view can advance, EPIC requests a bell, subject to [set beep](../set/beep.md) and terminal support. There is normally no confirmation message.

Additional words after the count are processed as subsequent window operations. `/window -scroll_forward` is a no-op. An invisible window's view can change without making it visible. Input selection is unchanged, and no IRC request is sent.

## Examples

```text
/window scroll_forward
/window 3 scroll_forward 10
/window scroll_forward 0 refresh
```

Advance by the default percentage, advance window 3 by ten display rows, or use default movement and then process pending updates.

## History

This operation first appeared in EPIC4-1.1.11.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_scroll_forward`, `window_scrollback_forward`, `window_scrollback_forwards_lines`, `window_scroll_lines_tester`, and `window_scrollback_forwards` implement arguments, percentage movement, and view selection; `recalculate_window_cursor_and_display_ip` automatically cancels caught-up scrollback.
- [include/config.h](../../include/config.h): defines the compiled scrollback ratio.
- [source/term.c](../../source/term.c): `term_beep` applies beep settings to the no-movement bell request.
