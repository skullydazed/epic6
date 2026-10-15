# previous_window

## Synopsis

```text
/bind <key> previous_window
```

## Description

Move the input focus to the next higher selectable window on the input screen. At the top of the screen, cycle back to the bottom. Windows marked with [window](../commands/window.md) `skip on` are passed over; the order is the windows' screen layout, not their reference numbers.

If there is only one visible window, or all other windows on the input screen are skipped, focus remains on the current window. The action also does nothing when there is no input screen. To replace the current visible window with an invisible one, use [swap_previous_window](swap_previous_window.md).

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It selects the destination as the current window, updates the screen's input focus and window displays, and triggers [on switch_windows](../on/switch_windows.md) when the current window changes. It does not send an IRC request.

The default bindings are `^[Op` and `^[[p`, where `^[` means Escape. Scripts can change them.

## Related command

[window previous](../window/previous.md) swaps in an invisible window selected by reference number. Use this binding action to cycle through windows already visible on the input screen.

## Example

```text
/bind ^X^P previous_window
```

With three selectable windows stacked on the input screen, pressing Control-X followed by Control-P cycles from the bottom to the middle, then the top, then back to the bottom.

See [bind](../commands/bind.md) for key sequence syntax and [next_window](next_window.md) to move in the opposite direction.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default bindings.
- [source/window.c](../../source/window.c): `previous_window`, `get_previous_window`, `make_window_current_by_refnum`, and `set_screens_current_window` implement selection, skip handling, wraparound, focus updates, and the window-switch hook; `windowcmd_previous` implements the separate invisible-window operation.
