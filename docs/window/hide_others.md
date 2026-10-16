# window hide_others

## Synopsis

```text
/window [<window>] hide_others
```

## Description

Like [window hide](hide.md), but attempt to hide every other visible window on the selected window's screen. With no window selector, keep the current window. Windows on other screens are unaffected.

When all the other windows can be hidden, the retained window expands to fill the screen. Hidden windows continue to exist and function normally, retaining their server, channels, queries, and stored output. Use [window show](show.md) to bring them back.

## Windows that remain visible

Windows with [window swappable](swappable.md) off are silently skipped. Other windows are hidden individually using the normal `hide` restrictions. In particular, a hide is refused when the number of visible windows, excluding those with **both** [window fixed](fixed.md) and [window skip](skip.md) on, is at most one. EPIC reports `You can't hide the last window.` for such attempts.

Consequently, `hide_others` can leave several windows visible. Changes already made are retained if a later hide is refused. The remaining windows share the screen space, normally giving freed output rows to non-fixed windows first.

If the selected window is invisible, this operation silently does nothing. If it is already the only visible window, there is nothing to hide.

## Selection and arguments

The selected window is protected from hiding, but the operation does not explicitly make it the screen's input window. Hiding the input window chooses a replacement using the screen's window history, falling back to the first remaining visible window. After the operation, subsequent operations in the same `/window` command still target the window you chose to retain, even if the input window is different.

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations. `/window -hide_others` is a no-op. Successful hides normally produce no confirmation message, and no IRC request is sent.

## Examples

```text
/window hide_others
/window 3 hide_others
```

Keep the current window, or window 3, while hiding eligible other windows on its screen. To restore window 7 afterward:

```text
/window show 7
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_hide_others` selects candidates and preserves the retained target; `hide_window` and `count_fixed_windows` enforce restrictions; `remove_window_from_screen`, `set_screens_current_window`, and `recalculate_windows` handle replacement input selection and resizing.
