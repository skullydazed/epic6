# windowctl

Inspect windows, create windows, and change selected window properties.

## Synopsis

```irc
$windowctl(refnum <refnum, name, or UUID>)
$windowctl(refnums)
$windowctl(refnums_by_priority)
$windowctl(refnums_on_screen <window refnum>)
$windowctl(new)
$windowctl(new_hide)
$windowctl(get <refnum> <field> [field argument])
$windowctl(set <refnum> <field> [value])
```

## Technical

Operations and field names are case-insensitive and accept prefixes, selecting the first matching branch. Use full names to avoid ambiguity: `ref` selects `refnum`, not `refnums`. Text arguments use function word parsing controlled by `/xdebug dword`; numeric arguments use permissive base-zero integer parsing, so nonnumeric text can become zero. In numeric window arguments, `0` means the current window. Only `refnum` resolves names and UUIDs directly; use its result with `get` or `set`. Hidden windows are included in lookup.

| Operation | Behavior |
| --- | --- |
| `refnum description` | Resolve a window reference, case-insensitive name, or UUID to its user reference; empty if unknown |
| `refnums` | List all window user references in registry traversal order |
| `refnums_by_priority` | List windows by descending last-visited priority |
| `refnums_on_screen window` | List windows on the specified window's screen, top to bottom; a hidden window selects the hidden-window list |
| `new` | Create a window on the current window's screen and make it current; it can be hidden if screen insertion fails or the current window is hidden |
| `new_hide` | Create a hidden window without explicitly selecting it as the current window |
| `get refnum field` | Read the property described below |
| `set refnum field value` | Change only one of the supported writable properties |

Creation returns the new user reference, or `-1` if creation fails, and invokes the window-create hook. These are local operations; they do not connect to an IRC server.

## Fields

Unless listed under setters below, fields are read-only through this interface. Use the [window command](../commands/window.md) for other changes.

| Field | Value |
| --- | --- |
| `refnum`, `name`, `uuid` | User reference, optional name, and fixed UUID string |
| `server`, `server_string` | Attached server reference (`-2` if none), and stored original server description |
| `priority`, `visible`, `screen` | Visit priority, visibility boolean, and screen reference (`-1` if hidden) |
| `top`, `bottom`, `columns`, `display_size`, `cursor` | Display geometry and output cursor position |
| `fixed`, `skip`, `swappable` | Window resizing, navigation, and swapping flags |
| `scrolladj` | Currently returns the `swappable` flag, rather than the scroll-adjustment setting |
| `change_line`, `scroll_lines`, `old_size`, `update`, `deceased` | Overwrite-line setting, scroll increment, previous display size, and internal update/deletion state |
| `notify`, `notify_name`, `notified`, `beep_always` | Hidden-output notification flag/name/state and beep behavior |
| `notify_level`, `window_level`, `lastlog_level` | Level-mask strings |
| `double` | Number of configured status bars minus one |
| `status_format`, `status_format1`, `status_format2` | Stored raw formats for status slots 0, 1, and 2 |
| `status_line`, `status_line1`, `status_line2` | Cached rendered results for those slots; may be empty before rendering |
| `display_buffer_size`, `display_buffer_max`, `display_counter` | Scrollback buffer count, maximum, and internal counter |
| `scrolling_distance`, `holding_distance`, `scrollback_distance` | Distances from the corresponding view to the insertion point |
| `hold_slider`, `hold_interval`, `indent` | Hold-view slider, held-count reporting interval, and continuation indentation flag |
| `channels`, `current_channel`, `waiting_channel` | Owned channels, selected channel, and pending joins |
| `query_nick`, `nicklist` | Current query target and the window's nick/query list |
| `lastlog_size`, `lastlog_max` | Current lastlog entry count and configured maximum |
| `logging`, `logfile`, `log_mangle`, `log_rewrite` | Logging flag, filename, mangling description, and rewrite text |
| `toplines`, `saved` | Requested topline count; both names read the same value |
| `topline n` | Stored topline text, with `n` from 1 through 9 |
| `activity_format n`, `activity_data n` | Activity format/data slots, numbered 0 through 10 |
| `current_activity` | Selected activity slot |
| `line n` | Stored scrollback text, counting backwards from the insertion point; `1` is the newest stored line |
| `last_server` | Compatibility placeholder, always `-2` |
| `noscrollcursor`, `scroll`, `last_lines_held` | Compatibility placeholders, always `-1` |
| `miscflags` | Compatibility placeholder, always `0` |
| `bind_channel` | Compatibility placeholder, always empty |

`line 0` normally reads the empty insertion sentinel. Negative offsets also select that point; an offset beyond available scrollback returns empty. Returned line text is denormalized to IRC formatting codes, rather than stripped to plain text. It reads stored scrollback, independently of the currently displayed hold/scrollback view.

## Setters

String values are the complete remaining text, without another dequoting pass. An omitted string supplies empty text, except where clearing is explicitly described below.

| Field | Effect and result |
| --- | --- |
| `notify_name text` | Store the notification name, or clear it when omitted; returns `1` |
| `indent number` | Assign the integer directly without boolean validation; returns empty even on success |
| `log_mangle flags` | Parse flags against the existing mask; omitted text clears the mask and description; returns empty even on success |
| `log_rewrite text` | Store the logging rewrite, or clear it when omitted; returns empty even on success |
| `topline n text` | Store topline 1–9 and request body redraw; returns `1`, or `0` for an out-of-range index |
| `activity_format n text`, `activity_data n text` | Store slot 0–10 and request status update; returns `1`, or empty for an invalid index |
| `current_activity n` | Select slot 0–10 and request status update; returns `1`, or empty for an invalid index |

Other setters return empty without changing the field, including `name`, `server`, status formats, geometry, and `toplines`. Setting topline text does not increase the requested topline count. Logging mangling uses the same flag parser described by [stripcrap](stripcrap.md), with the current mask as its starting value.

## Returns and restrictions

Results depend on the operation or property. Missing required arguments, invalid windows, unknown operations, and unknown fields generally return an empty string. An empty result can also be a valid unset string or a successful setter result; it is not a universal error indicator. Query fields expose local stored state, which need not reflect an already completed screen redraw or server action.

## Examples

```irc
$windowctl(refnum main)              /* Resolve a named window. */
$windowctl(refnums_on_screen 0)      /* Windows on the current window's screen. */
$windowctl(get 0 current_channel)    /* Current window's selected channel. */
$windowctl(get 0 line 1)             /* Newest stored scrollback line. */
$windowctl(set 0 notify_name alerts) /* Returns 1. */
```

## History

The function appeared in EPIC4-1.2.0. `swappable` appeared in EPIC4-1.2.2, `toplines` in EPIC5-0.0.3, and activity fields in EPIC5-0.0.6.

## Source

Registered and wrapped by `function_windowctl` in [source/functions.c](../../source/functions.c). The dispatch, window lookup, creation, traversal, property storage, and scrollback insertion are in [source/window.c](../../source/window.c). Formatting denormalization and logging flag parsing are in [source/ircaux.c](../../source/ircaux.c).
