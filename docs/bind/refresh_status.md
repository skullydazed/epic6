# refresh_status

## Synopsis

```text
/bind <key> refresh_status
```

## Description

Request an update of the status lines so they reflect current values. This is useful when [status_does_expandos](../set/status_does_expandos.md) is enabled and a variable or function used by [status_format](../set/status_format.md) has changed without another event triggering a status update.

This built-in action marks every window's status for recalculation, including windows on other screens and invisible windows. Normal window update processing rebuilds the status text and redraws visible status lines when that text has changed. An unchanged status line is not forcibly repainted; use [refresh_screen](refresh_screen.md) to repair physical display damage.

Status expandos, when enabled, are evaluated in each window's context. Physical drawing occurs only for visible windows while EPIC is in fullscreen mode and running in the foreground. If status updates are temporarily suppressed, processing is deferred until updates are allowed again.

The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment and does not itself send an IRC request or produce a separate text message.

## Example

```text
/bind ^X^S refresh_status
```

Press Control-X followed by Control-S after changing a value displayed by a status expando to request updated status text.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/window.c](../../source/window.c): `update_all_status_kb`, `update_all_status`, and `window_statusbar_needs_update` mark windows for updates; `update_all_windows` distinguishes changed text from forced redraws.
- [source/status.c](../../source/status.c): `make_status`, `redraw_status`, and `permit_status_update` implement expando evaluation, rendering, and deferred status updates.
