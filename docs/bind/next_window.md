# next_window

## Synopsis

```text
/bind <key> next_window
```

## Description

Move the input focus to the next lower selectable window on the input screen. At the bottom of the screen, cycle back to the top. Windows marked with [window](../commands/window.md) `skip on` are passed over; the order is the windows' screen layout, not their reference numbers.

If you use only one visible window and keep the rest invisible, this action has no effect. Use [swap_next_window](swap_next_window.md) to replace the current visible window with an invisible one. If all other windows on the input screen are skipped, focus remains on the current window. The action also does nothing when there is no input screen.

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It selects the destination as the current window, updates the screen's input focus and window displays, and triggers [on switch_windows](../on/switch_windows.md) when the current window changes. It does not send an IRC request.

The default bindings are Control-W (`^W`), `^[On`, and `^[[n`, where `^[` means Escape. Scripts can change them.

## Example

```text
/bind ^W next_window
```

With three selectable windows stacked on the input screen, pressing Control-W cycles from the top to the middle, then the bottom, then back to the top.

See [bind](../commands/bind.md) for key sequence syntax and [previous_window](previous_window.md) to move in the opposite direction.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default bindings.
- [source/window.c](../../source/window.c): `next_window`, `get_next_window`, `make_window_current_by_refnum`, and `set_screens_current_window` implement selection, skip handling, wraparound, focus updates, and the window-switch hook.
