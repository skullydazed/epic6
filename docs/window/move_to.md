# window move_to

## Synopsis

```text
/window [<window>] move_to <number>
```

## Description

Move the selected visible window to position `<number>` from the top of its screen, counting from 1. With no window selector, move the current window. Position 1 is topmost, position 2 is second, and a number at least as large as the visible-window count puts it at the bottom. Unlike [window move](move.md), this is an absolute position and does not wrap.

The window is removed from its old position and inserted at the destination. Intervening windows shift while retaining their order relative to one another. Positions count windows rather than display rows, including windows with [window fixed](fixed.md) or [window skip](skip.md) on. Heights, stored output, server associations, and visibility are unchanged. The screen's positions are recalculated and redrawn.

After an actual move, the selected window becomes its screen's input window and EPIC's global current window. Normal input history is updated, and a change of global current window invokes the `switch_windows` hook. Subsequent operations in the same `/window` command still target the selected window. No IRC request is sent, and successful execution normally produces no confirmation message.

## Arguments and limitations

`<number>` is required and must be an integer. A missing or nonnumeric argument reports `MOVE_TO: You must specify a number`. Following words are processed as subsequent window operations, including after this error. Use ordinary decimal integers; numeric conversion also interprets a leading zero as octal.

Zero or negative positions, invisible targets, and a screen with only one window are silently unchanged. If the window is already at the requested position, the helper returns without changing input selection. `/window -move_to` is a no-op. Movement stays on the existing screen.

Use an actual screen position where possible. Although the window-order code treats an oversized position as bottom, the current implementation passes the original number unchanged to a separate screen-order helper. That helper rejects positions above 1000 without the caller reporting its failure, so very large values can leave the two ordering records inconsistent. Avoid arbitrarily large sentinel values for the bottom position.

## Examples

```text
/window move_to 1
/window move_to 2
/window 7 move_to 3
```

Move the current window to the top or second position, or put window 7 third on its screen. If its screen has exactly three visible windows, the final example puts window 7 at the bottom.

## History

This operation first appeared in EPIC4-1.1.8.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_move_to`, `get_number`, and `move_window_to` implement parsing, insertion, bottom handling, and selection; `recalculate_window_positions` updates geometry and redraw requests.
- [source/screen.c](../../source/screen.c): `screen_window_place` maintains separate screen-order data and enforces `MAX_WINDOWS_ON_SCREEN` (1000).
- [source/ircaux.c](../../source/ircaux.c): `is_number` and `my_atol` validate and convert the position.
