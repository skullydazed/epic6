# set indent

## Synopsis

```text
/set indent [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

When on, long output lines are wrapped so that continuation text normally aligns with the start of the second word on the first row. Subsequent rows of the same logical line use the same indentation width. Word boundaries follow [word_break](word_break.md).

EPIC caps the indentation at one third of the window's display width. If the detected position is nonpositive, including text without a useful word boundary, it uses that one-third width instead.

The continuation prefix comes from [continued_line](continued_line.md). With indentation on, the prefix is fitted to the indentation width: it is padded with spaces when shorter and can be truncated when longer. It is not guaranteed to appear either in full or not at all. With indentation off, the prefix is still used at its own width, so disabling indentation does not necessarily remove the continuation marker.

## Existing and new windows

Changing this global setting updates the indentation flag of **all existing windows**, including hidden ones, overwriting individual [window indent](../window/indent.md) choices. New windows copy the global value. A later `window indent` command can override one window until another global assignment applies to all windows again.

The setting affects text when display rows are prepared. Its callback does not itself rebuild existing scrollback or request a redraw, so already wrapped rows need not immediately change. Use [window rebuild_scrollback](../window/rebuild_scrollback.md) to reformat retained output in a selected window.

This is local display formatting, not a change to the underlying IRC message, sent text, or normal pre-wrapping log text. Dumb-mode direct output does not use the full-screen wrapping path. Changing the setting sends no IRC request.

## Example

Enable indentation globally and reformat retained output in the current window:

```text
/set indent on
/window rebuild_scrollback
```

To change only the current window, use `/window indent on` instead. Restore the global default with `/set indent off`.

## Source references

- [source/vars.c](../../source/vars.c): `set_indent` copies the setting to every existing window.
- [source/window.c](../../source/window.c): `new_window` copies the default; `windowcmd_indent` provides the individual override.
- [source/screen.c](../../source/screen.c): `prepare_display` finds indentation, caps it, and fits the continuation prefix.
- [include/config.h](../../include/config.h): default off.
