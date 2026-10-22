# window screen_debug

## Synopsis

```text
/window [<window>] screen_debug
```

## Description

Print diagnostic information for the selected window's screen: its screen number and address, followed by occupied screen locations and their internal window references. With no selector, use the current window. The output describes the screen's attachment table, not just the selected window.

There are no operation arguments. This does not alter screen state. The handler tests the screen number as a boolean: zero reports that invisible-window dumping is unsupported; other values are passed to the dump helper, which silently returns if no screen exists. Thus a hidden window need not produce an explanatory message. `/window -screen_debug` runs the same diagnostic.

## Examples

```text
/window screen_debug
```

## Source references

`windowcmd_screen_debug` in [source/window.c](../../source/window.c) and `screen_window_dump` in [source/screen.c](../../source/screen.c).
