# set old_server_lastlog_level

## Synopsis

```text
/set old_server_lastlog_level [<levels>]
/set -old_server_lastlog_level
```

Default: `none`. With no value, [set](../commands/set.md) displays the current mask. Unsetting disables this server-switch level adjustment.

## Description

Controls level adjustment when [window server](../window/server.md) moves a window to a different server that is already connecting or connected. This matters when the moved window and windows already on the destination server claim overlapping output levels.

Despite its name, the setting affects the [window level](../window/level.md) routing mask, not the window's [lastlog_level](lastlog_level.md) recording mask. EPIC retains the moved window's existing routing levels, adds selected levels only if no window anywhere already claims them, and removes the moved window's resulting levels from other windows on the destination server. It does not first replace the moved window's mask with this setting.

Consequently, the default `none` adds no levels but still allows the moved window to take its existing levels away from other windows on that server. It does not mean that the moved window loses all levels. Setting `all` makes all unclaimed levels eligible for addition; it does not take already claimed levels unless the moved window already owns them too.

For example, if two windows on different servers each have level `all`, moving one onto the other's server with the default setting leaves the moved window's levels intact and clears those overlapping levels from the other window.

## When it applies

The server-switch branch applies this policy when the destination's state is after `reconnect` and before `eof`, including connection setup, registration, syncing, and active operation. Simply naming the window's existing server does not trigger it. Changing this setting alone does not redistribute levels.

Unsetting skips this adjustment in that branch and can leave duplicate levels on windows sharing a server. `none` and unset therefore have different effects. Separate registration-time assignment is controlled by [new_server_lastlog_level](new_server_lastlog_level.md).

## Levels

Use the level names described under [lastlog_level](lastlog_level.md), separated by commas or spaces. Names are case-insensitive, and each assignment constructs a fresh mask from empty. `all` selects all levels; `none` or `-all` clears it; a leading `-` excludes an individual level, as in `all,-snotes`.

Prefixes work for inclusion and exclusion, selecting the first matching level name; prefer full names because ambiguous prefixes are not diagnosed. Unknown names produce a warning and list valid levels, while recognized parts still apply. The stored setting is normalized to the resulting mask.

## Examples

Retain the default policy of adding no extra levels while resolving ownership on server switches:

```text
/set old_server_lastlog_level none
```

Skip this policy entirely:

```text
/set -old_server_lastlog_level
```

Neither command immediately changes existing window levels.

## History

`set old_server_lastlog_level` first appeared in EPIC5-0.0.3.

## Source references

- [source/lastlog.c](../../source/lastlog.c): `set_old_server_lastlog_mask` parses the selection or disables the policy when unset.
- [source/window.c](../../source/window.c): `windowcmd_server`, `window_change_server`, `renormalize_window_levels`, and `revamp_window_masks` implement switching and routing ownership.
- [include/server.h](../../include/server.h): server-state ordering used by the switch condition.
- [source/levels.c](../../source/levels.c): mask parsing and diagnostics.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration and default none.
