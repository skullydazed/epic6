# clear command

## Synopsis

```text
/clear [-all | -hidden | -visible]
```

## Description

`clear` moves the window's normal scrolling view to the end of its scrollback, leaving that view empty until new output arrives. It does not delete the scrollback lines; you can still view them by scrolling backward. [`unclear`](unclear.md) brings recent lines back into view.

With no selection option, only the current window is affected. The command also clears the affected window's notification flag and current activity indicator, then requests a window-body and status-bar redraw. It does not clear lastlog, delete saved logs, or send anything to an IRC server.

Use [`window`](window.md) `clear` to apply this operation to a specific window. The command is available in every build, requires no server connection, and only changes the display in fullscreen mode. In dumb/non-fullscreen mode, its clearing helper does nothing.

A held view or a view already scrolled backward has its own display position. `clear` does not explicitly unhold the window or reset those positions, so such a view can continue showing old lines.

## Options

| Option | Windows affected |
| --- | --- |
| `-all` | All windows. The unprefixed `all` form is also accepted. |
| `-hidden` or `-invisible` | Windows not attached to a screen. |
| `-visible` | Windows attached to a screen. |

Selection options are case-insensitive and recognized by the first character after their prefix, so `-a`, `-h`, `-i`, and `-v` work. When several selection options are supplied, the last recognized selection wins.

The shared clear/unclear parser also accepts `-noforce` (or `-n`), but it has no effect on `clear`; it controls `unclear` behavior only. The old `-unhold` flag is not supported: it reports `Unknown flag: -unhold`. Other unrecognized arguments also report `Unknown flag`, but do not cancel clearing. Apart from these diagnostics, the command produces no text output.

## Examples

To clear the current window, every window, or only hidden windows:

```text
/clear
/clear -all
/clear -hidden
```

To bring recent scrollback back into the current window's normal view:

```text
/unclear
```

## Source

See [source/commands.c](../../source/commands.c) for `e_clear()`, its command-name distinction between `clear` and `unclear`, and option parsing. [source/window.c](../../source/window.c) implements `clear_window()`, window selection, notification/activity changes, and `window clear`. [source/screen.c](../../source/screen.c) selects normal, held, or scrollback positions during redraw.
