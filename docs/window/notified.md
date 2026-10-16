# window notified

## Synopsis

```text
/window [<window>] notified [on|off|toggle]
/window [<window>] -notified
```

## Description

Manually set or clear a window's notified state. With no window selector, use the current window. `on` marks it notified, `off` clears the flag, and `toggle` reverses it. New windows start with the flag off. Without an argument, report its current value.

A notified window is included in the `%F` status expando, formatted by [set status_notify](../set/status_notify.md). `%F` lists refnums; `%{1}F` uses [window notify_name](notify_name.md), then the window name, falling back to its refnum. The list normally appears on the current window's status, or on other windows when [set show_status_all](../set/show_status_all.md) permits it.

The manual flag can be set for visible as well as invisible windows. The status list tests the flag itself, not visibility or [window notify](notify.md). This operation does not enable automatic notification, change the [window notify_level](notify_level.md) mask, beep, print an activity message, or invoke `window_notified`. It also does not change the separate activity indicator used by `%E`.

## Automatic changes

Normal output to a hidden window can set the flag when its level matches that window's notify mask and window notification processing is enabled. That transition invokes the `window_notified` hook and requests status updates. `window notify` controls the accompanying activity message, rather than whether the flag can be set.

Making the window visible through show or swap clears its notified flag. [window clear](clear.md) also clears it in fullscreen mode. Manually clearing a hidden window allows later qualifying output to notify again.

The manual handler does not explicitly request a status redraw, so the `%F` display can wait until another status update occurs; it need not change immediately when this command changes the flag.

## Output and arguments

Queries and valid changes report `Window NOTIFIED is ON` or `Window NOTIFIED is OFF`. Invalid values report `Value for NOTIFIED must be ON, OFF, or TOGGLE` and leave the state unchanged. A query or invalid value stops subsequent operations in the same `/window` command.

`/window -notified` clears the flag silently. Additional words after a valid value or the minus form are processed as subsequent window operations. The operation does not change visibility, input selection, or stored output, and sends no IRC request.

## Examples

```text
/window 3 notified on
/window 3 notified
/window 3 notified off
/window 3 -notified
```

Mark window 3 notified, report its flag, clear it with a report, or clear it silently.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_notified`, `get_boolean`, and `new_window` implement manual changes, reports, and defaults; `add_to_window_list`, `swap_window`, and `clear_window` clear the flag.
- [source/status.c](../../source/status.c): `status_notify_windows` implements `%F` and `%{1}F` listing and status visibility.
- [source/screen.c](../../source/screen.c): `add_to_window` implements automatic flag setting, `window_notified`, and activity messages.
