# toggle_stop_screen

## Synopsis

```text
/bind <key> toggle_stop_screen
```

## Description

Toggle hold mode in the current window by performing [window hold_mode](../window/hold_mode.md) `toggle`. This built-in action calls the window-command handler directly, then updates window displays. It changes the current window's hold state rather than the global default for new windows.

When turning hold mode on, EPIC creates a held view from the normal scrolling view and advances it by the window's hold-slider percentage of its output height, stopping at the newest output. As new output fills that view, further lines are retained in the buffer until you advance or release it. Server messages continue to be processed.

When turning hold mode off, EPIC removes the held view and requests a redraw and status update. An independent scrollback view can remain active, so disabling hold mode does not necessarily jump to the newest output; use [scroll_end](scroll_end.md) to advance that view.

The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment, leaves input text unchanged, and sends no IRC request. The window-command handler reports the new hold-mode value through its normal output.

## Example

```text
/bind ^X^H toggle_stop_screen
```

Press Control-X followed by Control-H to enable hold mode in the current window; press it again to disable hold mode. [send_line](send_line.md), normally Enter, can acknowledge and advance held output without disabling hold mode.

See [bind](../commands/bind.md) for key sequence syntax, [window hold_slider](../window/hold_slider.md) for the advance amount, and [unstop_all_windows](unstop_all_windows.md) to disable hold mode in every window.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/window.c](../../source/window.c): `toggle_stop_screen`, `windowcmd_hold_mode`, and `get_boolean` implement toggling and its output; view recalculation and `trim_scrollback` govern held display and retention.
- [source/screen.c](../../source/screen.c): window display selects between normal, held, and scrollback views.
