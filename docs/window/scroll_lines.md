# window scroll_lines

## Synopsis

```text
/window [<window>] scroll_lines [<number>]
```

## Description

Set how many display rows the selected window scrolls when new output overflows its normal view. With no selector, use the current window. New windows use `-1`, which inherits [set scroll_lines](../set/scroll_lines.md); a nonpositive global value effectively scrolls one row.

Use `-1` for the global default or a positive integer within the selected window's output height. Zero and values below `-1` are rejected. A value above the selected window's height produces a warning and stores the current window's output height, which can differ from the selected window's height. During scrolling, the effective amount is capped at the receiving window's height.

No argument reports the stored value or `<default>` and stops the operation chain. Valid changes report the resulting setting. `/window -scroll_lines` does nothing. This setting controls normal output scrolling, not manual [scroll_backward](scroll_backward.md) or [scroll_forward](scroll_forward.md) movement.

## Examples

```text
/window 3 scroll_lines 1
/window 3 scroll_lines -1
/window 3 scroll_lines
```

## Source references

`windowcmd_scroll_lines`, `new_window`, and `add_to_scrollback` in [source/window.c](../../source/window.c); scrolling in [source/screen.c](../../source/screen.c).
