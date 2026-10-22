# set blank_line_indicator

## Synopsis

```text
/set blank_line_indicator [<text>]
/set -blank_line_indicator
```

Default: unset. With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Marks unused display rows below the end of a window’s output when the window is repainted. Only the first byte of the string is written at the start of each unused row; use a single printable ASCII character. This does not replace empty lines in received text.

Changing the setting requests updates for all windows. Unsetting it leaves unused rows unmarked.

## Example

```text
/set blank_line_indicator ~
/set -blank_line_indicator
```

## Source references

- [source/screen.c](../../source/screen.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
