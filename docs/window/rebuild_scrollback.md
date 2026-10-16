# window rebuild_scrollback

## Synopsis

```text
/window [<window>] rebuild_scrollback
```

## Description

Request that the selected window's scrollback buffer be rebuilt, even if no rebuild is otherwise needed. With no window selector, use the current window. The request is handled at the next window-update point, normally at the end of the `/window` command.

A rebuild normally happens automatically when a visible window's column width changes, including a terminal resize or moving a window to a screen of another width. An explicit request is useful after changing wrapping settings such as [window indent](indent.md).

EPIC saves the normal, held, and scrollback view positions and the clear boundary, deletes the current display buffer, then reconstructs it from retained lastlog entries for that window that are still marked as needed for scrollback. It normalizes and wraps those entries using the current width and display settings. Reconstruction is internal; it does not replay each line visibly or append the old output to logfiles again.

## Retained history and positions

The result is limited by the retained lastlog data. If old display rows are no longer represented there, they disappear during rebuilding. Increasing [window lastlog](lastlog.md) afterward cannot recover deleted entries. Conversely, entries outside the [lastlog_level](lastlog_level.md) selection can still participate if they remain stored and marked as needed. Output explicitly abandoned by flushing is not restored merely because its lastlog entry still exists.

Positions are saved by their associated lastlog entry, not by an exact wrapped-row offset. Restoration chooses the first new row belonging to that entry. If a saved entry is unavailable, the corresponding view or boundary falls back to the new top of scrollback. Further normal-view adjustment, including [window scrolladj](scrolladj.md) behavior and the clear boundary, can change what is visible. A rebuild therefore attempts to preserve your place but does not guarantee an identical screen or an exact row position.

The window body and status are redrawn as needed. After rebuilding, EPIC invokes `window_rebuilt` with the window's user-visible refnum. Stored lastlog entries are used as input rather than being recreated by replay. No IRC request is sent.

## Arguments and limits

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations before the pending update is handled. Repeated requests before that update simply leave the rebuild flag set. `/window -rebuild_scrollback` is a no-op.

An invisible window can be rebuilt using its stored column width without making it visible. The operation normally produces no confirmation message; hooks can produce output. Use [window refresh](refresh.md) when you only want a redraw rather than reconstruction from lastlog.

## Examples

```text
/window rebuild_scrollback
/window 3 rebuild_scrollback
/window 3 indent on rebuild_scrollback
```

Rebuild the current window or window 3, or enable indentation in window 3 and request that retained output be reformatted.

## History

This operation first appeared in EPIC5-0.0.7.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_rebuild_scrollback`, `update_all_windows`, `window_check_columns`, `rebuild_scrollback`, `save_window_positions`, and `restore_window_positions` implement scheduling, automatic rebuilds, and position restoration.
- [source/lastlog.c](../../source/lastlog.c): `reconstitute_scrollback` selects retained entries marked as needed.
- [source/screen.c](../../source/screen.c): `add_to_window_scrollback` normalizes and wraps entries without routing them through ordinary output/logging.
