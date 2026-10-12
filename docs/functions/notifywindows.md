# $notifywindows()

## Synopsis

`$notifywindows()`

## Technical

Returns a space-separated list of user-visible window refnums whose `notified` flag is set. It traverses all windows, including hidden windows, and tests the flag directly. Arguments are ignored. There is no server filter, and the call does not clear notification flags.

Normally, output to a hidden window sets its flag when the output level matches that window's `notify_level` mask and window notifications are enabled for the output. [/xecho -f](../commands/xecho.md) suppresses this notification processing for its output. The [/window](../commands/window.md) `notify` toggle controls the separate “Activity in window” message; it is not required for the flag to be set or for this function to return the window.

The flag can also be set or cleared explicitly with `/window notified on` or `/window notified off`. Consequently, inclusion is not proof that the window is currently hidden or has received new output. Normal window display/swap handling clears the flag, and other window operations can clear it too.

## Practical

This exposes the same notification flags used by the `%F` status-line expando. The formatting differs: this function always returns space-separated numeric refnums, while the status formatter uses commas and can use names for its named variant. Use the returned list to display activity indicators or inspect window notification state in a script.

## Returns

Window refnums in window traversal order, separated by spaces, or the empty string if no flags are set. No labels, names, or status prefixes are included. The function sends no IRC request and changes no window state.

## Examples

`$notifywindows()` might return `2 5` when windows 2 and 5 have their notification flags set. It returns the empty string when all flags are clear.

For the current window:

```irc
window notified on
echo Notification flags: $notifywindows()
window notified off
```

The explicit flag can include a visible window; actual output depends on other flagged windows and intervening events.

Registration and `function_notifywindows()`: [source/functions.c](../../source/functions.c). Notification getters, setters, and window commands: [source/window.c](../../source/window.c). Hidden-output notification processing: [source/screen.c](../../source/screen.c). `%F` formatting: [source/status.c](../../source/status.c).
