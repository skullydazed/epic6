# window move

## Synopsis

```text
/window [<window>] move <number>
```

## Description

Move the selected visible window by `<number>` positions on its screen. With no window selector, move the current window. Positive numbers move down; negative numbers move up. Movement wraps at both ends of the screen.

Positions count windows, not display rows. EPIC reduces the offset modulo the number of visible windows before moving, so an offset equal to a whole number of screenfuls of windows makes no change. Windows with [window fixed](fixed.md) or [window skip](skip.md) on are still counted and do not block movement.

The window is removed from its old position and inserted at its destination. Intervening windows shift to close the gap and make room; their order relative to one another is preserved. Window heights, contents, server associations, and visibility are unchanged. The affected screen positions are recalculated and redrawn.

After an actual move, the moved window becomes its screen's input window and EPIC's global current window. Normal input-window history is updated, and a change of global current window invokes the `switch_windows` hook. Subsequent operations in the same `/window` command still target the selected window. No IRC request is sent, and successful movement normally produces no confirmation message.

## Arguments and limits

`<number>` is required and must be an integer. A missing or nonnumeric argument reports `MOVE: You must specify a number`, without moving the window. Additional words are processed as subsequent window operations, including after this error. Use ordinary decimal integers; numeric conversion also interprets a leading zero as octal.

An invisible target is silently unchanged. With one visible window, or an offset that reduces to zero, the operation does nothing and does not select a different input window. `/window -move` is also a no-op. Movement stays on the target's existing screen; use [window move_to](move_to.md) to choose an absolute position there.

## Examples

With three windows in screen order A, B, C and A selected:

```text
/window move 1
```

The order becomes B, A, C. Starting from A, B, C again, `move 2` produces B, C, A, while `move -1` also wraps A to the bottom. To move window 7 up one position:

```text
/window 7 move -1
```

## History

This operation already existed in ircII2.1.5g.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_move`, `get_number`, `move_window`, and `move_window_to` implement parsing, modulo wraparound, insertion, and selection; `recalculate_window_positions` updates geometry and redraw requests.
- [source/screen.c](../../source/screen.c): `screen_window_place` updates the screen's ordering data.
- [source/ircaux.c](../../source/ircaux.c): `is_number` and `my_atol` validate and convert the offset.
