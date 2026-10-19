# set status_notify

## Synopsis

```text
/set status_notify [<text>]
/set -status_notify
```

Default: ` (W: %F)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the list field's format rather than clearing any window's notification flag.

## Description

Sets the status-bar format for windows marked as having activity to notify you about. The field is represented by `%F` in [status_format](status_format.md). The first `%F` inside `status_notify` is replaced with a comma-separated list of user-visible window refnums before insertion into the outer line. If no windows are marked, the field is empty.

The outer `%{1}F` variant uses each marked window's notify name, then its ordinary name, falling back to its refnum. It uses the same wrapper with plain `%F` as the substitution point. Other status percent fields are not recursively interpreted inside the wrapper.

With [show_status_all](show_status_all.md) off, both variants appear only in the input window of each screen. Turning it on permits them in other windows too. The outer global or per-window format must include an applicable field.

## Notification state

Hidden-window output at a selected [notify_level](notify_level.md) can set the window's notified flag. The renderer lists marked windows across the client, not just those on the displaying window's server. It tests the flag rather than rechecking visibility or recent activity, so manually marked windows can also appear.

[Window notify](../window/notify.md) controls the extra activity message rather than this list's membership. [Window notified](../window/notified.md) can set or clear the flag explicitly. Normal window-selection/display operations can clear it as well. This field does not implement nickname online/offline monitoring.

Changing the text rebuilds status formats and requests updates. It does not clear flags, move windows, replay output, or send an IRC request. The list has a fixed internal text buffer and can be truncated when many long names are selected.

Ordinary command parsing can expand dollar expressions on assignment. [Status_does_expandos](status_does_expandos.md) can additionally expand the assembled status line; the shared wrapper helper escapes dollar signs and backslashes in the inserted list for that pass.

## Example

```text
/set status_notify Active: %F
```

An outer `%F` can then supply `Active: 2,4`. Use `%{1}F` in the outer format to prefer window names while retaining `%F` in this wrapper. `/set -status_notify` hides the list without acknowledging the windows.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, status rebuild callback, and default wrapper.
- [source/status.c](../../source/status.c): `status_notify_windows` gathers flagged windows, chooses names or refnums, and applies wrapper and visibility rules.
- [source/screen.c](../../source/screen.c): hidden-window activity marks notification state.
- [source/window.c](../../source/window.c): notified flags, explicit controls, and clearing during window operations.
