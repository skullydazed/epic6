# refresh_inputline

## Synopsis

```text
/bind <key> refresh_inputline
```

## Description

Redraw the input line. This is useful when another process writes to the terminal and corrupts the visible input line without changing its actual contents.

This built-in action redraws the input lines on all active screens, including their prompts and horizontal scrolling indicators, and restores each input cursor to its current logical position. It reevaluates prompt and indicator text and recalculates the visible section of a long line. The input text and logical cursor positions are preserved.

[refresh_screen](refresh_screen.md) redraws the input line together with the rest of the display and is normally the more useful choice for general display damage. Use `refresh_inputline` when you specifically want to redraw input without requesting a redraw of window bodies or status bars.

The action takes no arguments or options; extra text supplied with the binding is ignored. It has no default key assignment and sends no IRC request. It does nothing unless EPIC is in fullscreen mode and running in the foreground.

## Example

```text
/bind ^X^R refresh_inputline
```

Press Control-X followed by Control-R to restore the input display while keeping the text you are editing.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/input.c](../../source/input.c): `refresh_inputline` calls `redraw_input(-1, UPDATE_ALL)`; `redraw_input` iterates active screens, expands prompts and indicators, checks display geometry, and redraws input.
- [source/output.c](../../source/output.c): the separate `refresh_screen` binding requests a full display redraw.
