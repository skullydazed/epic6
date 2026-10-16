# window level

## Synopsis

```text
/window [<window>] level [<levels>]
```

## Description

Set which output levels the selected window claims for display routing. With no window selector, use the current window. With no level argument, report its current selection. Supply a comma-delimited list of level names, such as `public,crap`; see [set lastlog_level](../set/lastlog_level.md) for level descriptions.

Claiming a level removes it from every other window associated with the same server, including invisible windows. Windows on different servers can retain the same level. Removing a claim does not automatically assign it to another window.

This is a routing preference, not a filter that discards all other output. Explicit window destinations, channel ownership, [window add](add.md) nick targets, and [set current_window_level](../set/current_window_level.md) routing take priority. Otherwise EPIC looks for a window claiming the output's level on its server. For output without a server, a claim from any server can match. If no claim matches, output goes to the current window when it belongs to that server, otherwise to another window on the server, finally falling back to the current window.

Consequently, a window can still receive output outside its level selection. To suppress output, use an appropriate [on](../commands/on.md) hook. To control lastlog eligibility independently, use [window lastlog_level](lastlog_level.md).

## Level arguments

- An unprefixed list replaces the selection: `public,crap`.
- `+<list>` adds the listed levels to the existing selection: `+msg,notice`.
- `-<list>` removes the listed levels: `-joins,parts`.
- `all` selects all levels, and `none` clears the selection.
- Within a replacement list, exclusions can follow `all`: `all,-joins,-parts`.

The first sign applies to the whole list as an update operation. The remaining list is parsed into a mask from left to right. Names are case-insensitive and prefixes are accepted; prefer full names where a prefix could match more than one level. Only one whitespace-delimited argument is consumed. Subsequent words are processed as other window operations.

Unsupported names produce a warning and a list of valid levels. Recognized parts still apply. An unprefixed list containing only unsupported names therefore clears the selection. Queries and changes report `Window level is <levels>`. `/window -level` reports the selection without changing it.

## Initial and server-change behavior

The first window starts with all levels; later newly created windows start with none. When a server begins synchronizing, its current window can gain unclaimed levels from [set new_server_lastlog_level](../set/new_server_lastlog_level.md), whose compiled default is all. When `window server` moves a window to an already connected server, [set old_server_lastlog_level](../set/old_server_lastlog_level.md), default none, supplies additional reclaimable levels.

These settings do not simply replace the window's mask or force it to steal all listed levels. The reclaim helper adds a listed level only if no window anywhere currently claims it, then removes the resulting window's claims from other windows on the same server. Existing claims on the moving window are retained.

Changing levels does not move stored output, rebuild scrollback, make an invisible window visible, or send an IRC request. Normal routing by these masks is bypassed by the simplified output path outside fullscreen mode.

## Examples

```text
/window level public,crap
/window 3 level +msg,notice
/window 3 level -notice
/window 3 level
```

Claim public and miscellaneous output in the current window, add private-message and notice claims to window 3, remove its notice claim, and report its selection. Higher-priority routing rules can still direct those messages elsewhere.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_level`, `revamp_window_masks`, `new_window`, `renormalize_window_levels`, and `windowcmd_server` implement parsing, exclusive claims, initial masks, and server changes.
- [source/levels.c](../../source/levels.c): `str_to_mask` and `standard_level_warning` implement list parsing and unsupported-level reports.
- [source/screen.c](../../source/screen.c): `add_to_screen` implements routing precedence and fallbacks.
- [source/server.c](../../source/server.c): applies new-server reclaimable levels during synchronization.
- [include/config.h](../../include/config.h): defines the new-server and old-server defaults.
