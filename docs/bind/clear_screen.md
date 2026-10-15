# clear_screen

## Synopsis

```text
/bind <key> clear_screen
```

## Description

Clear the current input window's normal scrolling view so nothing is visible in it and the next line of output starts at the top of the window. This built-in action uses the same clearing operation as [clear](../commands/clear.md) with no options; it calls the window helper directly.

A held view or a view scrolled backward has its own display position. The action does not explicitly unhold the window or reset those positions, so old lines can remain visible until that view is released.

Clearing preserves the window's scrollback. Use [scroll_backward](scroll_backward.md) to view earlier lines, or [unclear_screen](unclear_screen.md) to bring recent lines back into the normal view. The action also resets the window's notification flag and current activity indicator and requests a window-body and status-bar redraw. It leaves the input text unchanged and sends nothing to an IRC server.

The action takes no arguments or options; extra text supplied with the binding is ignored. It only clears the display in fullscreen mode; its clearing helper does nothing in dumb/non-fullscreen mode.

The default binding is `^[.`: Escape followed by a period. Scripts can change it.

## Example

```text
/bind ^[. clear_screen
```

Press Escape followed by a period to clear the current window's normal view while retaining its earlier lines in scrollback.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Escape-period binding.
- [source/input.c](../../source/input.c): `my_clear_screen` calls `clear_window_by_refnum(0)`.
- [source/window.c](../../source/window.c): `clear_window`, current-window resolution, notification/activity changes, and display-position recalculation.
- [source/screen.c](../../source/screen.c): window redraw chooses between normal, held, and scrollback views.
- [source/commands.c](../../source/commands.c): `e_clear` uses the same clearing helper for `/clear`.
