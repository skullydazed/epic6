# window flush

## Synopsis

```text
/window [<window>] flush
```

## Description

Discard queued output below the bottom of the selected window's held view. With no window selector, use the current window. This is useful when [window hold_mode](hold_mode.md) has accumulated output you do not want to read.

The held page and older scrollback are retained. Flushing discards the later display rows and moves the normal scrolling view to the held view. It does **not** turn hold mode off or advance the held page. To disable holding, use `/window hold_mode off` explicitly.

If the window has no held view, EPIC reports `/WINDOW FLUSH doesn't do anything unless you're in hold mode`. Scrolling backward alone is not sufficient. If there are no queued rows below the held page, there is nothing to discard.

Pressing Enter to submit `/window flush` also performs the usual input-line hold advancement. Invoking it from a key binding does not itself perform that advancement, so new output can remain held. When targeting a different window, input-line advancement applies to the input window rather than automatically to the selected target.

Discarded rows are removed from this window's scrollback, and their associated lastlog entries are marked as unnecessary for scrollback rebuilding. This operation does not erase saved log files or send an IRC request. Use [window flush_scrollback](flush_scrollback.md) to discard the entire scrollback instead.

## Arguments

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations. `/window -flush` is a no-op. An invisible window can be flushed without making it visible.

## Examples

```text
/window flush
/window 3 flush
```

Discard queued output in the current window or window 3, respectively. To discard the queue and then turn off holding in window 3:

```text
/window 3 flush hold_mode off
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_flush`, `flush_scrollback_after`, `recalculate_window_cursor_and_display_ip`, and `windowcmd_hold_mode` implement queue removal, view bookkeeping, and independent hold-mode control.
- [source/lastlog.c](../../source/lastlog.c): `dont_need_lastlog_item` marks discarded output as unnecessary for scrollback rebuilding.
- [source/input.c](../../source/input.c): `send_line` handles hold advancement when submitting an input line.
