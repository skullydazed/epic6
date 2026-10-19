# set new_server_lastlog_level

## Synopsis

```text
/set new_server_lastlog_level [<levels>]
/set -new_server_lastlog_level
```

Default: `all`. With no value, [set](../commands/set.md) displays the current mask. Unsetting the variable disables this automatic level assignment.

## Description

Selects output levels that EPIC may assign to a server's current window when the server confirms registration with numeric `001`, including after reconnection. Despite its name, this setting affects the window's output-routing level mask, as configured by [window level](../window/level.md). It does not replace the window's [lastlog_level](lastlog_level.md) recording mask.

EPIC adds only selected levels that no existing window already claims. It preserves previously assigned levels rather than replacing the current window's mask with this setting. The search for existing claims examines all windows, including windows associated with other servers. A level already claimed elsewhere is therefore not added by this step.

This happens on successful server registration, not simply whenever [window server](../window/server.md) changes a window's server association. Setting the variable changes the policy for later registrations; it does not immediately redistribute levels or move existing output.

## Level syntax

Separate names with commas or spaces. Names are case-insensitive, and each assignment constructs a new mask from empty.

- `all` selects all levels as eligible for assignment.
- `none` or `-all` selects none.
- Prefix a level with `-` to exclude it, as in `all,-opnotes,-snotes`.

Prefixes are accepted for both inclusion and exclusion, using the first matching name. Ambiguous prefixes are not diagnosed, so prefer full names. Unknown names produce a warning and a list of valid levels; recognized parts still apply. The stored value is normalized to the resulting mask.

Available built-in names include `other` (alias `crap`), `publics`, `msgs`, `notices`, `walls`, `wallops`, `opnotes`, `snotes`, `actions`, `ctcps`, `invites`, `joins`, `nicks`, `topics`, `parts`, `quits`, `kicks`, `modes`, `operwall`, `syserr`, and `user1` through `user10`. Historical `dcc` and `notes` levels are not registered. Scripts can add levels through [levelctl](../functions/levelctl.md). A level's existence does not itself provide a corresponding IRC protocol event.

`none` leaves the feature enabled with an empty eligible mask; unsetting disables the feature. Neither choice clears any existing window levels. Use these choices when you intentionally leave levels unclaimed and do not want a later connection to claim them automatically.

## Examples

Allow unclaimed levels other than server and operator notices to be assigned on later registrations:

```text
/set new_server_lastlog_level all,-opnotes,-snotes
```

Disable automatic assignment:

```text
/set -new_server_lastlog_level
```

To control which messages count as visible lastlog entries instead, use [set lastlog_level](lastlog_level.md) or [window lastlog_level](../window/lastlog_level.md).

## Source references

- [source/lastlog.c](../../source/lastlog.c): `set_new_server_lastlog_mask` constructs the mask or disables the feature when unset.
- [source/server.c](../../source/server.c): `server_is_registered` applies it to the server's current window.
- [source/window.c](../../source/window.c): `renormalize_window_levels` adds unclaimed levels to the window routing mask, then normalizes window-level ownership.
- [source/levels.c](../../source/levels.c): level names, mask parsing, and diagnostics.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration and default all.
