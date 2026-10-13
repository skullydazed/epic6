# $status()

## Synopsis

`$status(<window refnum> <status slot>)`

## Technical

Returns cached, expanded status-bar text for the specified window. Both integer arguments are required; extra arguments are ignored. Window refnum `0` selects the current input window. A numeric user-visible or internal window refnum is accepted; a window name is not resolved. Integer parsing is permissive and nonnumeric text becomes zero.

The second argument selects a status **cache slot**, not an ordinal screen line:

| Slot | Status configuration that generates it |
| --- | --- |
| `0` | The single status bar |
| `1` | Upper status bar in double-status mode |
| `2` | Lower status bar in double-status mode |

The helper permits slots up to the window's current number of status bars: `0` and `1` in single mode, or `0`, `1`, and `2` in double mode. Slots belonging to a different configuration can be stale or unset. In particular, slot `1` is not the bottom line in double mode. See [/window](../commands/window.md) for status configuration.

The cached text is converted from EPIC's internal display representation to text with IRC formatting controls. It can contain padding and formatting; it is not the raw status-format template or necessarily plain text.

## Returns

The cached status string. Missing arguments, a nonexistent window, or a slot greater than the allowed bound returns empty. If the selected cache has never been populated, the current conversion helper returns the literal text `<denormalize_string was called with NULL>` rather than empty.

Negative slot numbers are not checked before indexing the cache and must not be supplied. They do not select a line from the end.

## Practical

Use this to inspect the last generated status for a window. It does not regenerate the status or force a redraw, so changes awaiting an update are not reflected. Hidden windows can have their statuses regenerated using the main screen's width; inactivity alone does not guarantee that a status is stale. Status-update suppression can defer regeneration.

The function does not change focus or status configuration and does not need an IRC connection. [$status_oneoff()](status_oneoff.md) is a separate interface for expanding a supplied status format.

## Examples

`$status(0 0)` reads the current input window's single-status cache.

For window 2 in double-status mode, `$status(2 1)` reads the upper status and `$status(2 2)` the lower status. Slot zero can contain an older single-status result.

`$status()` returns empty because both arguments are required.

Registration and `function_status()`: [source/functions.c](../../source/functions.c). `get_window_status_line()` and window lookup: [source/window.c](../../source/window.c). Cache generation and slot mapping: [source/status.c](../../source/status.c). `denormalize_string()`: [source/screen.c](../../source/screen.c).
