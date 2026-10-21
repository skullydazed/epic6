# Window activity handling

## Synopsis

```text
$windowctl(set <winref> current_activity <level>)
$windowctl(set <winref> activity_format <level> <text>)
$windowctl(set <winref> activity_data <level> <text>)
$windowctl(get <winref> current_activity)
$windowctl(get <winref> activity_format <level>)
$windowctl(get <winref> activity_data <level>)
```

Activity formatting provides script-controlled status text for a window through `%E` in [status_format](../set/status_format.md). Scripts can use it to implement colored activity indicators. See the supplied [activity script](../scripts/activity.md) for a fuller implementation.

## Levels, formats, and data

Each window has eleven slots, numbered `0` through `10`, and a current activity level. Level `0` supplies fallback format/data and also means that `%E` itself displays nothing. Levels `1` through `10` have no built-in severity meaning: scripts choose what they represent and when to select them.

At status regeneration, `%E` reads the activity level of the **window whose status is being drawn**. If nonzero, it expands the selected activity format with the selected activity data as `$*`. It does not automatically enumerate hidden windows or replace their refnums in a combined list. The `%F` notified-window list is a different mechanism, controlled by notification state and [window notify](../window/notify.md).

Format and data fall back independently: an unset or empty selected format uses slot `0`'s format; an unset or empty selected data string uses slot `0`'s data. If the fallback is also empty, it remains empty. Consequently, an empty selected value cannot override a nonempty fallback with a deliberately blank value.

New windows start at activity `0`, with no format or data values. Showing, swapping in, or clearing windows resets activity on those window paths; selecting a level is not permanent notification history. The core setters do not automatically choose the greatest severity or classify incoming messages. Those policies require scripts.

## Example

The following script statements configure window `1`; use an existing window refnum in your session:

```irc
@ windowctl(set 1 activity_format 0 $chr(3)03$$*$chr(15))
@ windowctl(set 1 activity_data 0 ordinary)
@ windowctl(set 1 activity_format 2 $chr(3)04<$$*>$chr(15))
@ windowctl(set 1 activity_data 2 important)
@ windowctl(set 1 current_activity 1)
```

The doubled dollars store a literal `$*` for later activity expansion. With level `1` selected, both values fall back to slot `0`, yielding green `ordinary`. Selecting level `2` instead uses the explicitly stored red `<important>` format/data:

```irc
@ windowctl(set 1 current_activity 2)
```

The window's active status format must contain `%E` to display this value. For a window using one status row, an example per-window format is:

```text
/window 1 status_format "[%R] %N %E"
```

This replaces that window's single-row format; double-row windows use their corresponding `status_format1` and `status_format2` settings instead. Formatting controls and terminal capabilities determine the actual colors.

## Results and limits

Successful setters return `1` and request a status update. Getters return the stored slot text or current level; slot getters do not apply the fallback rules used for display. Invalid levels outside `0`–`10`, invalid window references, and unsupported requests return an empty string. Use full attribute names to avoid abbreviation ambiguity. See [windowctl](../functions/windowctl.md).

The `%E` expansion is copied into an 80-byte result buffer, so long results are truncated. This is not an independent timer or a request to an IRC server: it is evaluated as the status is regenerated. Keep format expressions inexpensive and avoid side effects. Clearing activity with level `0` suppresses `%E` without deleting the saved formats and data.

For a combined activity list with severity tracking, use or study the [activity script](../scripts/activity.md). Its list is built in `status_user2` and displayed through `%2` or `%Y`; its classification and reset rules are script behavior, separate from `%F` and the low-level `%E` renderer.

## History and source

Window activity formatting first appeared in EPIC5-0.0.6.

- `source/window.c`: slot storage, setters/getters, fallback selection, and window-state resets.
- `source/status.c`: `%E` registration, selected-window activity expansion, and result buffer.
- `script/activity`: script-provided classification and combined status list.
