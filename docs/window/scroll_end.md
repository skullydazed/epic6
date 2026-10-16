# window scroll_end

## Synopsis

```text
/window [<window>] scroll_end
```

## Description

Move forward toward the end of the selected window's stored output, leaving scrollback or acknowledging held output as applicable. With no window selector, use the current window. See [window](../commands/window.md) for the different display views.

The operation uses the forward-scrolling helper and advances up to **999999 display rows** in one call, stopping at the end of available output. This is a large fixed step rather than an unconditional jump across an arbitrarily large buffer. It does not use [set scrollback_ratio](../set/scrollback_ratio.md).

If a scrollback view is active, only that view is advanced. Reaching the normal or held view cancels scrollback, but does not simultaneously advance the held position. If a held backlog remains, run `scroll_end` again to acknowledge it. Without an active scrollback view, a held view behind the normal scrolling view is advanced instead.

This does **not** turn [window hold_mode](hold_mode.md) off. New output can become held again. To both leave scrollback and stop holding future output, explicitly disable hold mode as well.

Unlike [window flush](flush.md), this operation does not discard the queued rows it advances over. Normal buffer trimming can resume once scrollback ends and no held backlog prevents it, so it does not guarantee that all older output remains indefinitely.

## Output and arguments

If neither scrollback nor an older held view can be advanced, EPIC requests a bell, subject to [set beep](../set/beep.md) and terminal support. Otherwise it recalculates view positions and requests window-body and status updates, without a confirmation message.

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations on the selected window. `/window -scroll_end` is a no-op. It can update an invisible window without making it visible, does not change input selection, and sends no IRC request.

## Examples

```text
/window scroll_end
/window 3 scroll_end
```

Advance the current window or window 3 toward the end of its scrollback or held output. To disable holding and leave scrollback in one command:

```text
/window hold_mode off scroll_end
```

A buffer more than 999999 rows behind the end can still require repeated calls.

## History

This operation first appeared in EPIC4-1.1.11.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_scroll_end`, `window_scrollback_end`, `window_scrollback_forwards_lines`, and `window_scrollback_forwards` implement the fixed step and view selection; `recalculate_window_cursor_and_display_ip` cancels scrollback when caught up; `trim_scrollback` controls later retention.
- [source/term.c](../../source/term.c): `term_beep` applies beep settings to the no-movement bell request.
