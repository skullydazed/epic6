# set current_window_level

## Synopsis

```text
/set current_window_level [<levels>]
/set -current_window_level
```

Default: no selected levels. With no value, [set](../commands/set.md) displays the current mask. Use `none` or `/set -current_window_level` to clear it.

## Description

Selects output levels that should prefer the current window for the output's server over windows selected by ordinary [window level](../window/level.md) rules. It is an output-routing preference, not a lastlog filter, a subscription request, or a rule that overrides every destination.

The current full-screen routing order gives explicit output-window selection, special no-level routing, and channel or nickname target associations priority over this setting. For example, output associated with a channel already owned by a window goes there before this mask is considered. Thus adding `public` does not pull all channel messages away from their channel windows.

If no earlier destination claims the line, and its level is selected here, EPIC uses the current window recorded for `from_server`, provided that window is valid. This can differ from the globally focused input window when several servers are in use. If that destination is unavailable, normal routing continues.

Dumb-mode output takes a separate current-window path and does not consult this mask.

## Level syntax

Use comma-separated or space-separated level names, such as `msgs,public,other`. Names are case-insensitive. `all` selects all levels; `none` or `-all` clears the mask. Prefix an individual level with `-` to remove it from the mask being constructed, for example `all,-public`.

Each assignment starts from an empty mask rather than editing the previous value. Unknown names produce a warning and a list of valid levels; recognized parts are still applied, and the stored value is normalized to the resulting mask. Prefer complete level names rather than depending on prefix matching. See [levelctl](../functions/levelctl.md) for the current level list.

Changing the setting affects subsequent routing without moving previously displayed text or changing per-window level ownership. It sends no IRC request.

## Example

Prefer the server's current window for otherwise unclaimed private-message output:

```text
/set current_window_level msgs
```

An explicit nickname target association can still take precedence. Disable this preference with:

```text
/set current_window_level none
```

## Source references

- [source/screen.c](../../source/screen.c): `add_to_screen` applies destination precedence, this mask, and ordinary window levels in that order.
- [source/lastlog.c](../../source/lastlog.c): `set_current_window_mask` parses and normalizes the setting.
- [source/levels.c](../../source/levels.c): mask syntax, name matching, and unsupported-level warnings.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration and unset default.
