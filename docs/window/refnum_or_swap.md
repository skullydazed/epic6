# window refnum_or_swap

## Synopsis

```text
/window [<window>] refnum_or_swap <destination>
```

## Description

Select the destination if it is visible, or attempt to swap it into the selected window's place if it is hidden. With no window selector, the swap source is the current window. The destination can be a refnum, [window name](name.md), or UUID, looked up across all windows; names and UUIDs are case-insensitive.

For a visible destination, this behaves like [window refnum](refnum.md): it becomes EPIC's global current window and its screen's input window. No swap occurs, even if it is on another screen, and swappability is not checked.

For a hidden destination, this calls [window swap](swap.md) using the selected source window. The source becomes hidden and the destination takes its screen position and available space, adjusted for status bars and toplines. Each window retains its own contents, channels, queries, and server association. The replacement's notified flag and activity indication are cleared.

In the hidden-destination branch, input selection transfers only if the source was its screen's input window, and global current selection transfers only if the source was globally current. An explicit selector can therefore swap a different window without making the destination globally current. This operation does not guarantee that every destination becomes both visible and current.

## Failures and subsequent operations

The hidden-destination branch can fail: the source must be visible, and both source and destination must have [window swappable](swappable.md) on. An invisible source reports `You can only SWAP an invisible window with a visible window.` An unswappable participant reports `Window <name-or-number> is not swappable`. These checks leave visibility unchanged.

**After a destination is found, subsequent window operations target it even if the swap fails.** The handler does not check the swap helper's result before returning that destination. It is therefore unsafe to assume that a following operation runs only after a successful swap.

A missing destination reports `REFNUM_OR_SWAP: Please specify a window refnum or name`; an unknown destination reports `REFNUM_OR_SWAP: No such window: <destination>`. Both stop subsequent operations without changing selection. The destination is consumed as one whitespace-delimited word; use its refnum or UUID for a name containing spaces.

There are no other options. Additional words are processed as subsequent window operations on the destination. `/window -refnum_or_swap` is a no-op. Successful selection or swapping normally produces no confirmation message and sends no IRC request.

## Examples

```text
/window refnum_or_swap 3
/window 7 refnum_or_swap chat
```

Select window 3 if visible, otherwise try to swap it with the current window. The second selects visible window `chat`, or tries to swap hidden `chat` with visible window 7. With an explicit source, successful swapping does not necessarily change global current selection.

Use `window refnum` when you want to select a hidden destination without changing visibility.

## History

This operation first appeared in EPIC4pre2.001.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_refnum_or_swap`, `get_window_from_args`, and `get_window_by_desc` implement lookup, branch selection, and subsequent targeting. `swap_window` enforces visibility/swappability and transfers geometry and selection; `make_window_current_by_refnum` and `set_screens_current_window` select visible destinations.
