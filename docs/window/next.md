# window next

## Synopsis

```text
/window [<window>] next
```

## Description

Swap the selected visible window with the eligible hidden window having the next higher user-visible reference number. With no window selector, use the current window. If no eligible hidden window has a higher refnum, wrap to the lowest eligible hidden refnum.

Candidates come from the global invisible-window list, across all servers. Windows with [window swappable](swappable.md) off or [window skip](skip.md) on are excluded. Refnums need not be consecutive; use [window number](number.md) to change their ordering.

This always [swaps](swap.md) a hidden window into the selected window's screen position. It does not cycle through visible split windows; use the [next_window](../bind/next_window.md) binding for that. The [swap_next_window](../bind/swap_next_window.md) binding uses this operation.

The original window becomes hidden and retains its contents and associations. The replacement keeps its own server, channels, and settings, and takes the available space, with its output height adjusted for its status bars and toplines. If the original was the screen's input window, the replacement takes that role. If it was EPIC's global current window, the replacement becomes current. When an explicit selector targets another visible window, the swap need not change global current selection.

The replacement's notification flag and activity indication are cleared. No window is destroyed and no IRC request is sent.

## Errors and arguments

If no eligible candidate exists, EPIC reports `There are no invisible windows` and stops subsequent window operations. This can happen even when hidden windows exist but all are skipped or unswappable.

The selected window must be visible and swappable. A hidden target can report `You can only SWAP an invisible window with a visible window.`; an unswappable target reports `Window <name-or-number> is not swappable`. These swap errors leave visibility unchanged and do not themselves stop subsequent operations. If candidate selection fails first, the no-invisible-windows message takes precedence.

There are no arguments or options specific to `next`. Additional words are processed as subsequent window operations using EPIC's resulting global current window, which can differ from an explicit swap target. `/window -next` also performs the operation; the handler does not use the null argument as a no-op. Successful swapping normally produces no confirmation message.

## Example

If window 2 is current and eligible hidden windows have refnums 5 and 9:

```text
/window next
```

Window 5 replaces window 2, and window 2 becomes hidden. Repeat to select window 9; from window 9, the next eligible hidden refnum wraps to 2.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_next` selects eligible hidden windows by user refnum and handles failure; `swap_window` implements geometry, visibility, selection, and notification changes. `swap_next_window` delegates the binding to this handler.
