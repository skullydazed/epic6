# unclear_screen

## Synopsis

```text
/bind <key> unclear_screen
```

## Description

Perform the normal [unclear](../commands/unclear.md) operation on the current window, bringing recent output back into its normal scrolling view. This built-in action calls the window helper directly with forced restoration enabled, as `/unclear` does with no options.

The action removes the saved clear boundary and moves the normal view backward from the end of scrollback by up to one window output height. This can restore lines hidden by [clear_screen](clear_screen.md), provided they are still retained in scrollback. If fewer lines remain, only those lines can be restored. The operation requests a window-body and status-bar redraw.

Held output keeps its own display position, and a view scrolled farther backward can remain visible instead of the restored normal view. A scrollback position no farther back than the normal or held view is cancelled during recalculation. The action does not explicitly unhold the window.

The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment. It leaves input text, notification/activity indicators, and stored output intact and sends no IRC request. Display repositioning requires fullscreen mode.

## Example

```text
/bind ^X^C unclear_screen
```

After clearing a window, press Control-X followed by Control-C to bring its most recent retained output back into the normal view.

See [bind](../commands/bind.md) for key sequence syntax. Use `/unclear -noforce` if you want restoration to respect the last clear boundary; this binding always removes that boundary.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/input.c](../../source/input.c): `input_unclear_screen` calls `unclear_window_by_refnum(0, 1)`.
- [source/window.c](../../source/window.c): `unclear_window_by_refnum`, `reset_window_clear_point`, `unclear_window`, and display-position recalculation implement restoration.
- [source/commands.c](../../source/commands.c): `e_clear` uses the same helper for `/unclear` with default force behavior.
