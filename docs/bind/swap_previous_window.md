# swap_previous_window

## Synopsis

```text
/bind <key> swap_previous_window
```

## Description

Perform the [window previous](../window/previous.md) operation on the current window, swapping it with an eligible invisible window. This built-in action calls the window-command handler directly, then updates window displays.

Selection uses window reference numbers: choose the eligible invisible window with the largest number less than the current window's number. If none has a smaller number, wrap to the eligible invisible window with the largest number. Candidates marked `skip on` or `swappable off` through [window](../commands/window.md) are excluded. Selection is global across invisible windows, rather than limited to a server.

The incoming window becomes visible in the outgoing window's screen position and becomes current. Input focus transfers when the outgoing window was the screen's input window. The outgoing window becomes invisible; its contents remain available. EPIC adjusts the incoming window's display size for its status lines and toplines, resets its notification/activity indicators, and requests redraws.

If no eligible invisible window is found, the action reports `There are no invisible windows to swap in` and makes no swap. If the outgoing window has swapping disabled, it reports `Window <name-or-number> is not swappable`. Swapping also requires the outgoing window to be visible; otherwise the helper reports `You can only SWAP an invisible window with a visible window.`

The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment and sends no IRC request.

## Example

```text
/bind ^X^P swap_previous_window
```

With current window 4 and eligible invisible windows 1, 2, and 7, pressing Control-X followed by Control-P swaps window 2 into view. Repeating selects 1; after that, selection wraps to 7 if it remains eligible and invisible.

See [bind](../commands/bind.md) for key sequence syntax, [swap_next_window](swap_next_window.md) for the opposite reference-number order, and [previous_window](previous_window.md) to move focus among windows already visible on the screen.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/window.c](../../source/window.c): `swap_previous_window` invokes `windowcmd_previous`; that handler selects invisible candidates and calls `swap_window`, which checks eligibility, transfers screen placement and focus, and requests redraws.
