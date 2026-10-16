# window flush_scrollback

## Synopsis

```text
/window [<window>] flush_scrollback
```

## Description

Clear the selected window's entire scrollback, removing all stored output from its display area. With no window selector, use the current window. This includes the currently visible output, older scrollback, and queued output waiting in hold mode.

The window starts with an empty output buffer. Its normal view is reset, scrollback mode ends, and [window hold_mode](hold_mode.md) is turned off. EPIC requests output-area and status-bar redraws. An invisible window is cleared without being made visible.

Unlike [window clear](clear.md), this operation deletes the stored display rows, so scrolling backward or using [window unclear](unclear.md) cannot restore them. Unlike [window flush](flush.md), it discards the whole buffer rather than only output below the held page, and it does not require hold mode.

The associated lastlog entries are marked as unnecessary for rebuilding scrollback; this does not itself delete those entries from lastlog. Saved log files and input text are unchanged. No IRC request is sent.

## Arguments and output

There are no arguments or options specific to this operation, and successful execution produces no confirmation message. Additional words are processed as subsequent window operations. `/window -flush_scrollback` is a no-op. Flushing an already empty window leaves it empty.

## Examples

```text
/window flush_scrollback
/window 3 flush_scrollback
```

Clear all stored display output from the current window or window 3, respectively. To clear window 3 and enable holding again:

```text
/window 3 flush_scrollback hold_mode on
```

## History

`window flush_scrollback` first appeared in EPIC5-0.0.7.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_flush_scrollback`, `flush_scrollback`, `get_window_hold_mode`, and `recalculate_window_cursor_and_display_ip` implement buffer replacement, view reset, and redraws.
- [source/lastlog.c](../../source/lastlog.c): `dont_need_lastlog_item` marks the old output as unnecessary for scrollback rebuilding.
