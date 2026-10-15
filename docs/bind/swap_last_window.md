# swap_last_window

## Synopsis

```text
/bind <key> swap_last_window
```

## Description

Swap the current visible window with the most recently hidden window, making that invisible window visible in the current window's place. If there are no invisible windows, or the current window is itself invisible, nothing happens.

This built-in action selects the first window in the global invisible-window list. Newly hidden windows are added to the front of that list, including the window hidden by this swap. Repeating the action therefore normally toggles between the same two windows. Selection does not skip windows marked `skip on` and does not search for another candidate when the selected window cannot be swapped.

Both windows must allow swapping through [window](../commands/window.md) `swappable`. If either has swapping disabled, the action reports `Window <name-or-number> is not swappable` and leaves the windows in place.

On success, the incoming window takes the outgoing window's screen position and becomes current; input focus transfers when the outgoing window was the screen's input window. EPIC adjusts the incoming window's display size for its status lines and toplines, clears its notification/activity indicators, and updates window displays. The outgoing window is retained as an invisible window.

The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment and sends no IRC request.

## Example

```text
/bind ^X^L swap_last_window
```

With one visible window and another recently hidden window, press Control-X followed by Control-L to swap them. Press the sequence again to bring the original window back, provided both remain swappable.

See [bind](../commands/bind.md) for key sequence syntax, [swap_next_window](swap_next_window.md) and [swap_previous_window](swap_previous_window.md) for reference-number-based selection, and [next_window](next_window.md) to change focus among already visible windows.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/window.c](../../source/window.c): `swap_last_window`, `add_to_invisible_list`, and `swap_window` implement selection, swappability checks, screen placement, focus transfer, and redraws.
