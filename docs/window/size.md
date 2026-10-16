# window size

## Synopsis

```text
/window [<window>] size [<rows>]
```

## Description

Set the selected window's scrolling output area to `<rows>` display rows. With no window selector, use the current window. Status bars and toplines are additional overhead and are not included in this number. Size zero leaves no output rows, but any status bars and toplines remain.

One other visible window on the same screen supplies or receives the difference; the operation does not resize every other window equally. To enlarge the target, EPIC searches downward, wrapping to the top, for the first other window with enough output rows to supply the entire increase. It does not combine rows from several donors. To shrink the target, the next visible window receives the freed rows, wrapping from bottom to top.

A [fixed](fixed.md) window can be explicitly resized. In the current implementation, the helper's fixed-window check also does not reliably exclude a fixed donor or recipient, so other fixed windows can be resized indirectly.

Stored output, window order, server associations, and input selection are preserved. The normal view adjusts to its new height and the screen is redrawn; [window scrolladj](scrolladj.md) affects whether growth reveals older retained rows. Use [window grow](grow.md) or [window shrink](shrink.md) for relative changes, or [window balance](balance.md) to equalize non-fixed windows. No IRC request is sent.

## Queries, parsing, and errors

Without a numeric argument, report `Window size is <rows>`. Unlike many numeric window operations, this uses decimal-prefix parsing: `size 10` requests ten rows, and `size 10refresh` requests ten rows then leaves `refresh` for the command dispatcher. A nonnumeric next word is not consumed by `size`, so `size describe` reports the height and then describes the window. Use separate words for clarity.

An invisible window can be queried, but attempting to resize it reports `You cannot change the size of invisible windows!`. With only one visible window, EPIC reports `Can't change the size of this window!`, even if the requested height equals its current height. The same message occurs when no single donor can supply a requested increase. A negative target height reports `Not enough room to resize this window!`. These failures leave sizes unchanged.

There are no other options. Remaining text is processed as subsequent window operations, including after a resize error. `/window -size` is a no-op rather than a query. Successful resizing normally produces no confirmation message.

## Examples

```text
/window size 10
/window 3 size 0
/window 3 size
```

Set the current output area to ten rows, remove window 3's output area while retaining its overhead, or report window 3's current output height. Resizing requires another visible window on the same screen.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_size`, `resize_window`, `recalculate_windows`, and `resize_window_display` implement queries, absolute resizing, row transfer, errors, and view adjustment.
- [source/ircaux.c](../../source/ircaux.c): `parse_number` uses decimal `strtol` and leaves unconsumed text for subsequent operations.
