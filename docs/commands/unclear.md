# unclear command

## Synopsis

```text
/unclear [-all | -hidden | -visible] [-noforce]
```

## Description

`unclear` redraws the current window with the most recent window-full of its scrollback in the normal scrolling view. This brings back lines hidden by [`clear`](clear.md). If fewer lines remain in scrollback, only those lines can be shown; the amount restored uses the window's current display height.

By default, the command removes the saved clear boundary, then moves backward from the end of scrollback by up to one display height. `-noforce` preserves that boundary and stops there, so lines preceding the most recent `clear` are not brought back. Immediately after clearing, `unclear -noforce` can therefore leave the normal view empty.

Only the current window is affected unless a selection option is given. The operation requests a window-body and status-bar redraw. It does not delete or recreate scrollback, clear lastlog, change saved logs, or send an IRC request. Unlike `clear`, it does not reset the window's notification flag or activity indicator.

Held output retains its own display position. A view scrolled farther backward can also remain visible instead of the restored normal view; a scrollback position no farther back than the normal or held view is reset during recalculation. This command does not explicitly unhold the window.

## Options

| Option | Effect |
| --- | --- |
| `-all` | Unclear all windows. Unprefixed `all` is also accepted. |
| `-hidden` or `-invisible` | Unclear windows not attached to a screen. |
| `-visible` | Unclear windows attached to a screen. |
| `-noforce` | Keep the saved clear boundary and stop when it is reached. |

Options are case-insensitive and recognized by the first character after their prefix: `-a`, `-h`, `-i`, `-v`, and `-n` work. When multiple window selections are supplied, the last recognized selection wins. Unknown arguments report `Unknown flag` but do not cancel the operation. Otherwise the command produces no text output or function return value.

The command is available in every build and requires no server connection. Display repositioning requires fullscreen mode. For a specific window, [`window`](window.md) `unclear` uses the same repositioning operation but preserves the clear boundary, like `unclear -noforce`.

## Examples

```text
/unclear
/unclear -all
/unclear -hidden
/unclear -visible -noforce
```

These restore the current window, all windows, hidden windows, or visible windows without crossing their clear boundaries, respectively.

## Source

[source/commands.c](../../source/commands.c) implements `e_clear()`, its `clear`/`unclear` distinction, and option parsing. [source/window.c](../../source/window.c) implements `unclear_window()`, selection helpers, clear-boundary resetting, view recalculation, and `window unclear`. [source/screen.c](../../source/screen.c) chooses normal, held, or scrollback positions when redrawing.
