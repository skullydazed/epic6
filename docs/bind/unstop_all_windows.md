# unstop_all_windows

## Synopsis

```text
/bind <key> unstop_all_windows
```

## Description

Perform [window hold_mode](../window/hold_mode.md) `off` in every window, releasing windows held through [toggle_stop_screen](toggle_stop_screen.md) or another hold-mode operation. This built-in action includes invisible windows and windows on other screens or servers, then updates window displays.

For each window with hold mode enabled, EPIC removes the held view, recalculates display positions, and requests a window-body redraw and status update. Windows already out of hold mode remain out of it. The global default hold-mode setting for new windows is unchanged.

An independent scrollback view can remain active, so releasing hold mode does not necessarily show the newest output immediately. Use [scroll_end](scroll_end.md) in a window to advance its scrollback view.

The window-command handler reports `Window HOLD_MODE is OFF` for each window processed, including windows whose hold mode was already off. The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment, leaves input text unchanged, and sends no IRC request.

## Example

```text
/bind ^X^U unstop_all_windows
```

Press Control-X followed by Control-U to disable hold mode throughout the client. Use `toggle_stop_screen` instead when you want to change only the current window.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/window.c](../../source/window.c): `unstop_all_windows` iterates all windows and invokes `windowcmd_hold_mode`; that handler removes held views, while `get_boolean` produces the setting report.
