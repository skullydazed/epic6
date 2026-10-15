# refresh_screen

## Synopsis

```text
/bind <key> refresh_screen
```

## Description

Request a complete redraw of the screen. This is useful when another process writes to the terminal and corrupts the visible display without changing EPIC's stored contents.

This built-in action sets a redraw flag, which the event loop processes. EPIC clears each active screen, marks window bodies and status bars for redraw, updates the windows, and redraws the input lines. It also checks the main screen's terminal size and recalculates its windows if necessary.

The action restores the display from EPIC's current state; it does not erase input text or discard the windows' stored output. It takes no arguments or options; extra text supplied with the binding is ignored. It sends no IRC request and produces no separate text message.

The default binding is Control-L (`^L`). Scripts can change it. The full window display is available in fullscreen mode; input-line redraw additionally requires EPIC to be running in the foreground.

## Example

```text
/bind ^L refresh_screen
```

Press Control-L after unwanted terminal output has overwritten parts of the display to redraw windows, status bars, and input lines.

See [bind](../commands/bind.md) for key sequence syntax and [refresh_inputline](refresh_inputline.md) to redraw only input lines.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-L binding.
- [source/output.c](../../source/output.c): `refresh_screen` sets `need_redraw`; `redraw_all_screens` clears active screens and requests the component redraws.
- [source/irc.c](../../source/irc.c): the event loop processes `need_redraw`.
- [source/window.c](../../source/window.c) and [source/input.c](../../source/input.c): window/status redraw and input-line redraw.
