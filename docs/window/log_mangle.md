# window log_mangle

## Synopsis

```text
/window [<window>] log_mangle ["<rules>"]
```

## Description

Set formatting transformations for the selected window's [log](log.md), overriding [set mangle_logfiles](../set/mangle_logfiles.md) when the window's transformation mask is nonzero. With no selector, use the current window. This does not enable logging or change displayed output.

Rules are one quoted space-separated list or one comma-separated word. They modify the existing window mask: a rule enables a transformation, and a leading `-` removes it. Supported names are `ALL_OFF`, `ALT_CHAR`, `ANSI`, `BLINK`, `BOLD`, `COLOR`, `ESCAPE`, `ND_SPACE`, `NORMALIZE`, `OTHER`, `REVERSE`, `UNDERLINE`, and `UNPRINTABLE`. `ANSI` is an alias for `NORMALIZE`. `ALL` selects the standard group, excluding `ESCAPE`, `OTHER`, and `UNPRINTABLE`; `NONE` or `-ALL` clears the mask. Unknown rules are ignored.

No argument clears the window mask; it does not report it. A zero mask, including `NONE`, falls back to global logfile transformations rather than forcing unmodified logging. [set no_control_log](../set/no_control_log.md) can also strip unprintable characters. `/window -log_mangle` does nothing.

## Examples

```text
/window 3 log_mangle "NONE COLOR BOLD"
/window 3 log_mangle
```

## Source references

`windowcmd_log_mangle` in [source/window.c](../../source/window.c), `parse_mangle` and `add_to_window` in [source/screen.c](../../source/screen.c), and `add_to_log` in [source/log.c](../../source/log.c).
