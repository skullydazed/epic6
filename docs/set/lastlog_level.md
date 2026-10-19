# set lastlog_level

## Synopsis

```text
/set lastlog_level [<levels>]
/set -lastlog_level
```

Default: `all`. With no value, [set](../commands/set.md) displays the current mask. Use `none` or `/set -lastlog_level` to clear it.

## Description

Selects the output levels marked as visible, counted records when new output enters a window's lastlog. Any combination can be selected. These records are available to the [lastlog](../commands/lastlog.md) command and count toward the window's [lastlog](lastlog.md) retention limit.

Changing this setting updates the current window's lastlog mask and the global default copied by new windows. It does not update every existing window. Use [window lastlog_level](../window/lastlog_level.md) for an individual window without changing the global default.

Previously stored entries retain the visibility assigned when they were recorded. A mask change does not retroactively hide, expose, or delete those entries. Output outside the mask can still be stored internally for display reconstruction, so `none` is not a guarantee that no message text remains in memory. It does not suppress live display or file logging.

## Level syntax

Separate names with commas or spaces. Names are case-insensitive. Each assignment starts with an empty mask; it does not incrementally edit the previous value.

- `all` selects all levels.
- `none` or `-all` clears the mask being constructed.
- A leading `-` removes an individual level, as in `all,-opnotes,-snotes`.

The parser accepts prefixes for both inclusion and exclusion and takes the first matching level name; ambiguous abbreviations are not diagnosed as such. Prefer full names. Unsupported names produce a warning and list valid levels, while recognized parts still apply. The stored setting is normalized to the resulting mask.

Current built-in names include `other` (also `crap`), `publics`, `msgs`, `notices`, `walls`, `wallops`, `opnotes`, `snotes`, `actions`, `ctcps`, `invites`, `joins`, `nicks`, `topics`, `parts`, `quits`, `kicks`, `modes`, `operwall`, `syserr`, and `user1` through `user10`. A level name's presence does not imply a corresponding protocol event still exists; for example, the old `wall` hook is removed. Historical `dcc` and `notes` levels are not registered here.

Scripts can add levels and aliases through [levelctl](../functions/levelctl.md); use `levelctl(levels)` or the unsupported-level diagnostic to inspect the available names. Changing this setting sends no IRC request.

## Example

Count all new output except server and operator notices in the current window and future windows:

```text
/set lastlog_level all,-opnotes,-snotes
```

This does not erase previously recorded notice entries. To exclude all levels from the visible count for future entries, use `/set lastlog_level none`.

## Source references

- [source/lastlog.c](../../source/lastlog.c): `set_lastlog_mask` normalizes the setting and updates the current window; `add_to_lastlog` assigns visibility and counted size.
- [source/levels.c](../../source/levels.c): built-in level names, aliases, mask syntax, prefix matching, and warnings.
- [source/window.c](../../source/window.c): new windows copy the global mask; per-window mask assignment does not retag old entries.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and default all.
