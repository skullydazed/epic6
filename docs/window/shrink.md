# window shrink

## Synopsis

```text
/window [<window>] shrink <lines>
```

## Description

Decrease the selected window's scrolling output height by `<lines>` display rows. With no window selector, use the current window. Another visible window on the same screen receives those rows, leaving the terminal size unchanged. Status-bar and topline row counts are unchanged.

For an ordinary positive shrink, the recipient is the next visible window below the target, wrapping from the bottom to the top. The current resize helper can choose that recipient even when [window fixed](fixed.md) is on; fixed status does not reliably prevent this indirect enlargement. A fixed target can also be explicitly shrunk.

The target can be reduced to zero output rows, but not below zero. Stored output, window order, server associations, and input selection are preserved. The normal display view adjusts to the smaller area and the screen is redrawn. The recipient's growth can reveal older retained rows according to [window scrolladj](scrolladj.md).

Use [window grow](grow.md) to enlarge a window, [window size](size.md) for an absolute output height, or [window balance](balance.md) to equalize non-fixed windows. No IRC request is sent, and successful resizing normally produces no confirmation message.

## Arguments and errors

`<lines>` is required and must be an integer. A missing or nonnumeric argument reports `SHRINK: You must specify a number`. Use ordinary decimal integers; numeric conversion also interprets a leading zero as octal.

Zero makes no size change, although another visible window is still required. A negative value attempts to grow the target instead, requiring one other window with enough output rows to supply that increase; prefer `window grow` for that purpose.

An invisible target reports `You cannot change the size of invisible windows!`. With only one visible window, EPIC reports `Can't change the size of this window!`. The same message occurs for a negative shrink when no single other window can supply the growth. Shrinking below zero reports `Not enough room to resize this window!`. These failures leave sizes unchanged.

There are no other options. Additional words are processed as subsequent window operations, including after an error. `/window -shrink` is a no-op.

## Examples

```text
/window shrink 3
/window 7 shrink 2
```

Remove three output rows from the current window or two from window 7, giving them to another window on the same screen.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_shrink` negates the parsed amount; `get_number` and `resize_window` implement validation, recipient selection, row transfer, and errors. `recalculate_windows`, `recalculate_window_positions`, and `resize_window_display` update geometry and views.
