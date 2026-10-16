# window unclear

## Synopsis

```text
/window [<window>] unclear
```

## Description

Pull recent retained output into the selected window's normal scrolling view, eliminating empty space at the bottom when enough output is available. The next line of normal output then causes the window to scroll. With no window selector, use the current window.

This complements [window clear](clear.md), but respects the boundary recorded by clearing. It moves backward from the end of stored output by at most one windowful of display rows, stopping at the oldest retained row or the clear boundary. If fewer rows are available, the remaining space stays empty. Immediately after clearing, the boundary is at the end of stored output, so `/window unclear` can leave the normal view empty instead of restoring the older lines.

To remove that boundary and restore recent retained output in the current window, use [unclear](../commands/unclear.md) without `-noforce`. Scrolling backward can also reveal output before the boundary; neither operation can recover output already discarded from scrollback.

A held view or a view scrolled backward can continue showing older output. This operation changes the normal scrolling position without advancing the held position or disabling [hold mode](hold_mode.md). Recalculating the views cancels scrollback if its position is no longer older than the normal or held view.

## Effects and limitations

The operation requests window-body and status-bar redraws without a confirmation message. It retains scrollback and lastlog, leaves input text and saved log files unchanged, and sends no IRC request. It can update an invisible window without making it visible. The helper does nothing outside fullscreen mode.

There are no arguments or options specific to this operation. Additional words are processed as subsequent [window](../commands/window.md) operations. `/window -unclear` is a no-op.

## Examples

```text
/window unclear
/window 3 unclear
```

Fill the current window's normal view, or window 3's, with recent output after its clear boundary. To restore recent output in the current window regardless of that boundary:

```text
/unclear
```

## History

This operation first appeared in EPIC4pre1.038.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_unclear`, `unclear_window`, `clear_window`, and `recalculate_window_cursor_and_display_ip` implement the operation, clear boundary, and view recalculation; `unclear_window_by_refnum` implements forced restoration for `/unclear`.
- [source/commands.c](../../source/commands.c): `e_clear` handles `/unclear`, including its default forced restoration and `-noforce` option.
- [source/screen.c](../../source/screen.c): `repaint_window_body` selects the normal, held, or scrollback view to display.
