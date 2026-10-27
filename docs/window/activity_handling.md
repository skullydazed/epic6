# Window activity handling

## Synopsis

```text
$windowctl(SET <window> CURRENT_ACTIVITY <number>)
$windowctl(SET <window> ACTIVITY_FORMAT <number> <text>)
$windowctl(SET <window> ACTIVITY_DATA <number> <text>)
$windowctl(GET <window> CURRENT_ACTIVITY)
$windowctl(GET <window> ACTIVITY_FORMAT <number>)
$windowctl(GET <window> ACTIVITY_DATA <number>)
```

These [windowctl](../functions/windowctl.md) fields let scripts select and format per-window activity text for the `%E` [status-format field](../set/status_format.md). They can support colorized activity indicators, including irssi-like designs, but scripts must implement event classification and any combined window list.

## Activity slots and rendering

Each window has **11 slots, numbered 0 through 10**. The original “0 to 11” range was incorrect. Each slot stores an activity format and activity data; a separate `CURRENT_ACTIVITY` value selects a slot. Scripts choose what levels 1–10 mean. Slot 0 supplies fallback values, but selecting current activity **0 disables `%E` output**, even if slot 0 contains a format.

When rendering `%E` for a window with nonzero current activity, EPIC:

1. Uses the selected slot's nonempty format, falling back to slot 0 when it is unset or empty.
2. Independently uses the selected slot's nonempty data, falling back to slot 0 when it is unset or empty.
3. Expands the selected format with that data as `$*`, and places the result in the status line.

An empty selected-slot value cannot override a nonempty slot-0 default. Without a usable format the expansion is empty. New windows start at activity 0 with unset formats/data. GET returns the raw stored slot value, not its inherited rendering value. Valid SET operations return `1` and request a status update; an index outside 0–10 returns empty. Current `%E` rendering copies into an 80-byte buffer, limiting the result to 79 bytes before the terminator.

Formats and data do not have different persistence rules; changing data more often than formats is simply a useful scripting convention. Ordinary output does not assign meaningful activity severities automatically. Core operations such as showing/swapping a hidden window into view or clearing it can reset its current activity to 0; scripts may implement additional resets.

## How this differs from %F

The original described `%E` as iterating hidden windows and replacing every entry in `%F`. **Current `%E` renders only the activity state of the window whose status line is being generated.** It does not itself require hidden status, inspect the notified flag, or build a list of other windows.

`%F` separately collects windows with the built-in notified flag, normally set by qualifying output while hidden, and formats their refnums through [status_notify](../set/status_notify.md). Other status maps can use window names. [Window notify](notify.md) controls the separate “Activity in window” message; turning it off does **not** disable the notified flag or remove a window from `%F`.

For a ready-made aggregate activity list, see the [activity script](../scripts/activity.md). It builds its list through `STATUS_USER2`, displayed by `%2` or `%Y`, and separately configures `%E` for a window label. Adding `%E` alone does not load that script or reproduce its list.

## Configure formats and data

This corrected form of the original creation hook consistently uses WINDOW_CREATE's only argument, `$0`, as the new window's refnum:

```irc
on #-window_create -110 * {
    @ windowctl(SET $0 ACTIVITY_FORMAT 0 $chr(3)03$$*$chr(15));
    @ windowctl(SET $0 ACTIVITY_FORMAT 1 $chr(3)04$$*$chr(15));
    @ windowctl(SET $0 ACTIVITY_FORMAT 2 $chr(3)07<$$*>$chr(15));
    @ windowctl(SET $0 ACTIVITY_DATA 0 booya);
    @ windowctl(SET $0 ACTIVITY_DATA 2 hazmat);
};
```

`$chr(3)` inserts IRC color control and `$chr(15)` resets formatting. `$$*` preserves a literal `$*` in the stored format for expansion later. The color numbers are 03 (green), 04 (red), and 07 (orange). Actual appearance depends on the terminal and client display configuration. The hook configures future windows; apply the same operations to existing refnums if needed. It intentionally leaves the new current activity at 0 until an event selects a nonzero level.

Add `%E` to the applicable global or per-window status format to display the selected window's result. For a window configured as above:

| Selected activity | Format source | Data source | Result |
| --- | --- | --- | --- |
| `0` | Not rendered. | Not rendered. | Empty. |
| `1` | Slot 1: red `$*`. | Slot 0: `booya`. | Red `booya`. |
| `2` | Slot 2: orange `<$*>`. | Slot 2: `hazmat`. | Orange `<hazmat>`. |
| `3` | Slot 0: green `$*`. | Slot 0: `booya`. | Green `booya`. |

For example, `@ windowctl(SET 3 CURRENT_ACTIVITY 2)` selects slot 2 for window 3. This does not make its text appear automatically in another window's `%E`. The original worked examples disagreed with their own setup: slot 1 is red, and slot 2 is orange with angle brackets; neither color control makes the text bold by itself.

## Classify events with hooks

Levels can represent event categories, colors, or priorities. This adaptation preserves the original idea of public messages raising activity to 2 and joins raising it to 4:

```irc
alias raise_window_activity (channel, level) {
    @ :win = winchan($channel);
    if (win > 0 && windowctl(GET $win CURRENT_ACTIVITY) < level) {
        @ windowctl(SET $win CURRENT_ACTIVITY $level);
    };
};
on #-public -110 * {raise_window_activity $1 2};
on #-public_other -110 * {raise_window_activity $1 2};
on #-public_msg -110 * {raise_window_activity $1 2};
on #-join -110 * {raise_window_activity $1 4};
```

The channel is `$1` in these events; `winchan()` resolves it in the current event's server context, and the guard skips an unresolved channel. All three ordinary public-message hooks are included because messages in a noncurrent or outsider-classified channel can use a different hook from PUBLIC. Other hooks and suppression can prevent those specific events from running.

The comparison preserves the **highest selected numeric level**, rather than representing whichever event happened last. With the preceding formats, level 4 falls back to slot 0; set a distinct slot-4 format/data if you want it to look different. These hooks neither aggregate hidden windows nor maintain a reset policy. The bundled activity script is a more complete example of those separate responsibilities.

## History and source validation

The original records the first release as **EPIC5-0.0.6** and carries revision identifier `window_activity_handling.txt,v 1.3 2007/02/19 03:20:46 jnelson Exp`. Repository commit `c45703a3` introduced per-window activity levels on August 7, 2005, during the preceding development tree.

- [source/window.c](../../source/window.c): 11-slot storage, defaults, fallback getters, WINDOWCTL GET/SET range checks, update requests, and activity resets.
- [source/status.c](../../source/status.c): `%E` registration and per-window rendering, result-buffer size, and independent `%F` collection.
- [source/screen.c](../../source/screen.c): hidden-output notification flags and separate activity messages.
- [source/functions.c](../../source/functions.c), [source/hook.c](../../source/hook.c), and [source/parse.c](../../source/parse.c): WINCHAN registration/lookup and the referenced event arguments/dispatch.
- [script/activity](../../script/activity): explicit aggregate list and separate `%E` configuration.
