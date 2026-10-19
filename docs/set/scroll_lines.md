# set scroll_lines

## Synopsis

```text
/set scroll_lines [<number>]
```

Default: `1`. With no number, [set](../commands/set.md) displays the current value. The generic integer parser rejects negative and nonnumeric values.

## Description

When new output fills a window, EPIC scrolls its display upward to make room. This setting controls how many display rows are cleared at the bottom during each such scroll. One gives smooth line-by-line movement; a larger value moves text in chunks and leaves more space for following output.

This is a global fallback used by windows without a positive [window scroll_lines](../window/scroll_lines.md) override. New windows start with that fallback enabled rather than copying a fixed count. Changing the setting therefore affects subsequent scrolling in existing and future windows that use the default, while positive per-window overrides remain independent.

The count applies to wrapped display rows, not necessarily whole logical messages. It controls automatic scrolling as output arrives, not the distance moved by manual scrollback commands or the scrollback retention limit.

## Limits and effects

If the supplied count exceeds the current window's display height, EPIC reports the maximum and stores that height instead. When scrolling another window, or after resizing, the effective count is also capped at that destination window's current display height.

Zero does not disable scrolling. Assigning it displays `You cannot turn SCROLL off. Gripe at me.` but leaves zero stored; scrolling code treats a nonpositive fallback as one row. Use a positive value for predictable configuration. Windows with no display rows do not scroll visible output.

Changing the setting does not immediately scroll or erase a window. Previously displayed text can remain in scrollback, subject to its separate retention rules. This setting sends no IRC request.

## Examples

```text
/set scroll_lines 3
```

Windows using the fallback scroll three rows at a time, capped by their height. Restore line-by-line scrolling with:

```text
/set scroll_lines 1
```

A window with its own positive override continues to use that value. `/window scroll_lines -1` returns the selected window to this global fallback.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): integer registration and default one.
- [source/window.c](../../source/window.c): `set_scroll_lines` checks the assignment; new windows default to `-1`; `add_to_scrollback` uses the selected count for logical display advancement.
- [source/screen.c](../../source/screen.c): physical scrolling selects the per-window value or global fallback and caps it to display height.
