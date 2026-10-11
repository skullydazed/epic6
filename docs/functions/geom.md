# geom function

## Synopsis

`$geom()`

`$geom(<window>)`

## Technical

Returns the dimensions of the screen containing the selected window, as `<columns> <lines>`. With no argument, it uses the client's global current window; `0` also selects that window. An explicit argument can identify a window by refnum, name, or UUID. UUID and name lookup is exact and case-insensitive, and takes precedence over numeric lookup. Only the first argument word is used; normal function parsing honors [/xdebug dword](../commands/xdebug.md) for quoted names.

These are the full screen's dimensions, including the space occupied by all windows, status lines, and input. They are not the selected window's scrollback display height. Windows on the same screen report the same dimensions. The function queries EPIC's recorded dimensions and does not resize or select a window.

## Practical

Use the first returned number to decide how wide to format script output. The default follows the global current window, rather than necessarily the screen that last received keyboard input. Hidden windows have no screen and cannot supply geometry.

## Returns

Two space-separated integers: screen width in columns, then screen height in lines. An unknown window or a window without an associated screen returns the empty string.

## Examples

`$geom()` might return `80 24` for an 80-column, 24-line terminal. `$geom(0)` queries the same current window. `$geom(main)` queries the screen containing a window named `main`, if it exists and is visible.

```irc
@ screen_width = word(0 $geom())
```

Check that `$geom()` returned a value before relying on the extracted width.

## History

First appeared in EPIC3pre7. Support for the window argument first appeared in EPIC4-1.1.8.

## Source

Registration and `function_geom()`: [source/functions.c](../../source/functions.c). Window lookup and `get_window_geometry()`: [source/window.c](../../source/window.c). Recorded screen dimensions: [source/screen.c](../../source/screen.c).
