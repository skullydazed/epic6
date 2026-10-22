# window delete_kill

## Synopsis

```text
/window [<window>] delete_kill
```

## Description

Close the selected window's secondary screen and then attempt to destroy the selected window. Other windows on that screen become hidden. Subsequent window operations target the resulting current window. With no selector, use the current window.

This combines [delete](delete.md) with window destruction, allowing the last window on a secondary screen to be removed after the screen closes. Unlike [kill](kill.md), this handler does not check [killable](killable.md). Deletion discards the selected window's buffers and settings; saved log files remain on disk.

The main screen and the invisible screen cannot be closed. A screen-close refusal does not stop the subsequent window-deletion attempt, which is still subject to the ordinary visible-window safety restriction. Do not use this as a harmless screen-close test.

There are no operation arguments. `/window -delete_kill` does nothing.

## Examples

```text
/window 3 delete_kill
```

## Source references

`windowcmd_delete_kill`, `unlink_window`, and `delete_window_contents` in [source/window.c](../../source/window.c); `kill_screen` in [source/screen.c](../../source/screen.c).
