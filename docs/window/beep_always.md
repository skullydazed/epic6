# window beep_always

## Synopsis

```text
/window [<window>] beep_always [on|off|toggle]
```

## Description

Control whether bell characters in output routed to an invisible window request a terminal beep and a notification in the current window. With no window selector, operate on the current window. The default for a new window is off.

When off, this special invisible-window beep handling is disabled. When on, output containing a bell character (Control-G, byte `0x07`) normally produces `Beep in window number` and requests a beep. The audible or visual terminal effect still depends on [set beep](../set/beep.md), internal beep suppression, and terminal capabilities; this setting does not override them. The text notification can appear even with `beep` off.

The setting does not generate a beep for every message and does not control ordinary bell processing in visible windows. It supplements the window's activity notification rules rather than replacing them. When the same output also generates an activity notification, that notification can change the displayed message to `Activity in window number`.

Output that suppresses window notifications, such as [xecho](../commands/xecho.md) `-f`, bypasses this invisible-window notification and beep check.

## Arguments and output

`on` enables the setting, `off` disables it, and `toggle` reverses it. With no value, report the current setting without changing it. Successful changes and queries print `Window BEEP_ALWAYS is ON` or `Window BEEP_ALWAYS is OFF`. Invalid values report `Value for BEEP_ALWAYS must be ON, OFF, or TOGGLE` and leave the value unchanged.

`/window -beep_always` is a no-op; use `beep_always off` to disable it. The built-in operation changes local window behavior and sends no IRC request.

## Example

```text
/window 3 beep_always on
/window 3 beep_always
```

Enable bell notifications for window 3, then inspect the setting. It takes effect through this special path when window 3 is invisible and receives output containing a bell.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_beep_always`, `get_boolean`, and window initialization implement the setting and its reports.
- [source/screen.c](../../source/screen.c): output handling checks invisible-window bell characters, notification suppression, and activity notifications.
- [source/term.c](../../source/term.c): `term_beep` checks the global beep setting and suppression flag before emitting the terminal bell capability.
- [source/commands.c](../../source/commands.c): `xecho -f` suppresses window notifications.
