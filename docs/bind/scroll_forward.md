# scroll_forward

## Synopsis

```text
/bind <key> scroll_forward
```

## Description

Move forward toward newer output in the current window's scrollback, one page at a time. The page size is [scrollback_ratio](../set/scrollback_ratio.md) percent of the window's output height, rounded down to whole lines, with a minimum of one line. The default ratio is 50, or about half a window. Ratios below 1 are treated as 1 and ratios above 100 as 100 for this calculation.

If scrollback is active, this built-in action advances that view. When it catches up with the held or normal scrolling view, scrollback ends automatically. If scrollback is not active but the held view is behind the normal scrolling view, the action advances the held view instead. It does not turn hold mode off. When neither view can advance, it requests a terminal beep and makes no change.

Use [scroll_backward](scroll_backward.md) or [scroll_start](scroll_start.md) to begin browsing older output. [scroll_end](scroll_end.md) advances toward the end in one large step; repeated `scroll_forward` actions can also leave scrollback when they catch up.

This operation reviews the window's scrollback display buffer. The [lastlog](../commands/lastlog.md) command provides a separate way to search and print retained events.

The action requests a window-body redraw and status update. It takes no arguments or options; extra text supplied with the binding is ignored. To request a specific number of lines, use [window scroll_forward](../window/scroll_forward.md) `<lines>`. It does not change the input text or send an IRC request.

## Default bindings and example

Defaults include Escape followed by lowercase `n` (`^[n`), `^[OG`, `^[[G`, `^[O6~`, and `^[[6~`. EPIC also binds the terminal's `key_npage` sequence when available, normally Page Down. Scripts can change these bindings.

```text
/bind ^[n scroll_forward
```

Press Escape followed by `n` while browsing older output to move toward newer lines by the ratio-based amount.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default bindings, and terminal Page Down binding.
- [source/window.c](../../source/window.c): `scrollback_forwards`, `window_scrollback_forward`, `window_scrollback_forwards`, and the line-count tester implement movement and held-view selection; `recalculate_window_cursor_and_display_ip` cancels caught-up scrollback.
- [include/config.h](../../include/config.h): default scrollback ratio.
