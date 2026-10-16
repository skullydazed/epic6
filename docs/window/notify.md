# window notify

## Synopsis

```text
/window [<window>] notify [on|off|toggle]
/window [<window>] -notify
```

## Description

Control whether activity in the selected hidden window produces an `Activity in window <refnum>` message in the global current window. With no window selector, use the current window. New windows default to off. `on` enables the message, `off` disables it, and `toggle` reverses the setting. Without an argument, report it.

The message occurs when output reaches an invisible window, matches [window notify_level](notify_level.md), and changes its [window notified](notified.md) flag from off to on. It is not repeated for every line while the flag remains on. Making the window visible through show or swap clears that flag; manually clearing it permits later qualifying output to notify again.

This setting controls the activity **message**, not the automatic notified flag itself. With `notify off`, qualifying hidden output can still mark the window notified, invoke the `window_notified` hook, and make it appear in the `%F` status list. Enabling `notify` after the flag is already on does not itself emit an activity message. Turning it off does not clear the flag.

This has no relation to the nickname-presence [notify](../commands/notify.md) command. It is also distinct from [window beep_always](beep_always.md), which can produce a bell and `Beep in window <refnum>` for hidden output containing a bell character.

## Limits and output

Only invisible-window output triggers this notification path. Output to a visible window, even one that is not selected or is held, does not trigger it. Suppressed output does not reach this stage. [xecho](../commands/xecho.md) with `-f` disables the window notification path for that output. The normal path is also bypassed outside fullscreen mode.

Queries and valid changes report `Window NOTIFY is ON` or `Window NOTIFY is OFF`. Invalid values report `Value for NOTIFY must be ON, OFF, or TOGGLE` and leave the setting unchanged. A query or invalid value stops subsequent operations in the same `/window` command.

`/window -notify` turns the setting off silently. Additional words after a valid value or the minus form are processed as subsequent window operations. Changing this setting does not change visibility, input selection, routing levels, or stored output, and sends no IRC request.

## Examples

```text
/window 3 notify on
/window 3 notify_level msg,notice
/window 3 notify
/window 3 -notify
```

Enable activity messages for window 3, limit qualifying output to private messages and notices, report the setting, or disable messages silently. Window 3 must be hidden and not already notified for new qualifying output to emit the activity message.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_notify`, and `get_boolean` implement the default, arguments, and reports; `add_to_window_list` and `swap_window` reset the notified flag when showing a window.
- [source/screen.c](../../source/screen.c): `add_to_window` implements hidden-output matching, flag transitions, hooks, and activity/beep messages.
- [source/status.c](../../source/status.c): `status_notify_windows` tests the notified flag independently of this setting.
- [source/commands.c](../../source/commands.c): `xechocmd` implements `xecho -f` notification suppression.
