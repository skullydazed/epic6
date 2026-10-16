# window scrolladj

## Synopsis

```text
/window [<window>] scrolladj [on|off|toggle]
```

## Description

Choose how the selected window's normal scrolling view uses extra space when its output height grows. With no window selector, use the current window. New windows default to on.

With `on`, the traditional EPIC behavior, EPIC moves the top of the normal view backward to expose recently scrolled-off output in the added rows. With `off`, the traditional ircII behavior, it normally leaves that top position unchanged, leaving extra blank space at the bottom instead of exposing older rows. `toggle` reverses the setting; no argument reports it.

The backward adjustment stops at the oldest retained output or the saved [clear](clear.md) boundary. If too little history is available, enabling adjustment cannot fill every added row. This setting does not change the requested window size, preserve deleted history, or determine how many rows scroll during normal new output; see [window scroll_lines](scroll_lines.md) for that.

## View and rebuild interactions

The growth adjustment applies to the normal scrolling view. A separate held or scrollback view can still be the one you see. Shrinking a window adjusts the normal view as needed regardless of this flag. Additional normal-view fitting also occurs when its distance from the end is at least the new output height, so `off` is not an absolute lock on the top row.

The flag also affects restoration after [window rebuild_scrollback](rebuild_scrollback.md): when on, EPIC fits the normal view to recent retained output even if it would otherwise leave blank space. The saved clear boundary still limits that fitting. Width changes can trigger a rebuild automatically.

Changing the flag alone does not resize the window, reposition its view, rebuild scrollback, or explicitly request a redraw. Its effect is seen on later resizing or rebuilding. The resize-display helper does nothing outside fullscreen mode.

## Output and arguments

Queries and valid changes report `Window SCROLLADJ is ON` or `Window SCROLLADJ is OFF`. Invalid values report `Value for SCROLLADJ must be ON, OFF, or TOGGLE` and leave the setting unchanged. A query or invalid value stops subsequent operations in the same `/window` command.

Additional words after a valid value are processed as subsequent window operations. `/window -scrolladj` is a no-op. The flag can be changed for invisible windows without showing them or changing input selection. No IRC request is sent.

## Examples

```text
/window scrolladj off
/window 3 scrolladj on
/window 3 scrolladj
/window 3 scrolladj toggle
```

Choose the blank-space behavior in the current window, restore adjustment in window 3, report its flag, or reverse it.

## History

This operation first appeared in EPIC4-1.2.5.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_scrolladj`, and `get_boolean` implement defaults and reports; `resize_window_display` applies growth adjustment and mandatory view fitting; `restore_window_positions` and `unclear_window` implement rebuild fitting and the clear boundary.
