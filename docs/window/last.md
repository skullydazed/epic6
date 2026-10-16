# window last

## Synopsis

```text
/window [<window>] last
```

## Description

Switch between the current and previous input windows on a screen. This operation is for visible split windows; the corresponding operation for hidden windows is [window swap](swap.md) `last`.

Each screen records its input window and a last window: the window that most recently gave up input selection on that screen. `window last` makes the last window the input window and records the old input window as last. Repeating it normally switches back and forth between those two windows.

With no window selector, use the current window's screen. An explicit selector chooses the screen containing that window; it does not itself make that window the old input window. The destination also becomes EPIC's global current window, so subsequent operations in the same `/window` command apply to it.

If the recorded last window no longer exists, is deceased, or is no longer on that screen, EPIC falls back to the top visible window on the screen. Removing windows can also update the saved history. If the destination is already the screen's input window, input selection does not change; with only one visible window, this operation therefore normally has no visible effect.

Window order, sizes, and visibility are unchanged. The operation can select a window with [window skip](skip.md) on; it does not filter history or the fallback by that flag. A change of global current window invokes the `switch_windows` hook. No IRC request is sent, and successful execution normally produces no confirmation message.

## Arguments and edge cases

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations. Selecting an invisible window as the screen reference silently leaves selection unchanged, and subsequent operations use EPIC's current window. `/window -last` is a no-op.

## Example

Suppose windows 1, 2, and 3 are visible on one screen. After you switch input from window 2 to window 3, window 2 is last. Then:

```text
/window last
```

Window 2 becomes the input and current window, and window 3 becomes last. Repeat the command to return to window 3.

```text
/window 7 last
```

Select the previous input window on the screen containing window 7, provided window 7 is visible.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_last` selects the screen; `set_screens_current_window` validates history, chooses the fallback, and updates input selection; `make_window_current_by_refnum` changes global selection and invokes the switch hook. `remove_window_from_screen` updates history when removing windows.
- [source/screen.c](../../source/screen.c): initializes each screen's last-window reference to no window.
