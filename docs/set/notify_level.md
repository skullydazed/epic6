# set notify_level

## Synopsis

```text
/set notify_level [<levels>]
/set -notify_level
```

Default: `all`. With no value, [set](../commands/set.md) displays the current mask. Use `none` or `/set -notify_level` to clear it.

## Description

Sets the default notification levels copied when a new window is created. Changing this setting also changes the current window's notification mask, which may be surprising; it does not update every existing window. Use [window notify_level](../window/notify_level.md) to configure another window individually without changing the default.

The mask selects which output levels qualify as activity in a hidden window. When qualifying output reaches a hidden window that is not already marked notified, EPIC marks it, runs [on window_notified](../on/window_notified.md), and requests a status update. The status format's `%F` field can show notified windows. A window visible on a screen does not qualify through this hidden-window path simply because it is not current.

[Window notify](../window/notify.md) controls the additional `Activity in window ...` message; the notified flag is set independently of that toggle. Once flagged, further matching output does not repeat the transition until the flag is cleared. Notification-suppressed output, such as [xecho](../commands/xecho.md) with `-f`, bypasses this path. Hidden-window [beep_always](../window/beep_always.md) processing is separate.

Changing the mask does not clear an existing notification or replay old output. It does not filter message display, alter lastlog recording, or control nickname online/offline monitoring.

## Levels

Use the same level vocabulary as [lastlog_level](lastlog_level.md). Separate names with commas or spaces. Names are case-insensitive, and each assignment builds a new mask from empty rather than modifying the previous selection.

- `all` selects all levels.
- `none` or `-all` clears the mask being built.
- A leading `-` excludes a level, as in `all,-joins,-parts`.

The parser accepts prefixes for inclusion and exclusion and chooses the first matching level name. Prefer full names because ambiguous prefixes are not diagnosed. Unsupported names produce a warning and a list of valid levels; recognized parts still apply. The stored value is normalized to the resulting mask.

Common levels include `publics`, `msgs`, `notices`, `actions`, `ctcps`, `joins`, `parts`, and `modes`. See [lastlog_level](lastlog_level.md) for the built-in list; scripts can add levels through [levelctl](../functions/levelctl.md).

## Example

Select private messages and CTCP actions for the current window and as the default for future windows:

```text
/set notify_level msgs,actions
```

Other existing windows keep their current masks. To prevent future qualifying activity transitions for the current window and future windows, use `/set notify_level none`; already notified windows retain their flags.

## Source references

- [source/lastlog.c](../../source/lastlog.c): `set_notify_mask` parses the default and updates the current window.
- [source/window.c](../../source/window.c): new windows copy the default; per-window masks and notification flags are managed separately.
- [source/screen.c](../../source/screen.c): hidden-window level matching, notification transition, hook, and activity message.
- [source/levels.c](../../source/levels.c): mask syntax and diagnostics.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration and default all.
