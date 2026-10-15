# scroll_backward

## Synopsis

```text
/bind <key> scroll_backward
```

## Description

Move the current window's scrollback view backward toward older output. This built-in action uses the same scrolling helper as [window scroll_backward](../window/scroll_backward.md) with no line-count argument; it calls the helper directly.

The distance is [scrollback_ratio](../set/scrollback_ratio.md) percent of the window's output height, rounded down to a whole number of lines, with a minimum of one line. The default ratio is 50, or about half a window. For this calculation, ratios below 1 are treated as 1 and ratios above 100 as 100.

If scrollback is already active, movement starts at its current position. Otherwise, it starts at the held view when that view is older than the normal scrolling view, or at the normal scrolling view. The action activates the scrollback view and requests a window-body redraw and status update, so older output normally becomes visible immediately. It stops at the oldest retained line; invoking it again when already there requests a terminal beep and leaves the view unchanged.

The action takes no arguments or options; extra text supplied with the binding is ignored. To request a specific number of lines, use `window scroll_backward <lines>`. The binding does not change the input text or send an IRC request.

## Buffered output

New output continues to accumulate while the scrollback view is active. Automatic scrollback trimming is suspended during that time, so a busy window left in scrollback can consume increasing memory. This does not recover lines already trimmed before you entered scrollback.

The `%K` status-format field displays the [status_scrollback](../set/status_scrollback.md) text while scrollback is active. Use [scroll_forward](scroll_forward.md) to move toward newer output or [scroll_end](scroll_end.md) to leave scrollback.

## Default bindings and example

Defaults include Escape followed by lowercase `p` (`^[p`), `^[OI`, `^[[I`, `^[O5~`, and `^[[5~`. EPIC also binds the terminal's `key_ppage` sequence when available, normally Page Up. Scripts can change these bindings.

```text
/bind ^[p scroll_backward
```

Press Escape followed by `p` to move back by the ratio-based amount in the current window.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default bindings, and terminal Page Up binding.
- [source/window.c](../../source/window.c): `scrollback_backwards`, `window_scrollback_backward`, `window_scrollback_backwards`, the line-count tester, and `trim_scrollback` implement movement and retention.
- [source/status.c](../../source/status.c): `status_scrollback` implements `%K`.
- [include/config.h](../../include/config.h): default scrollback ratio.
