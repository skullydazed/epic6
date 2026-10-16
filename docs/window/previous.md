# window previous

## Synopsis

```text
/window [<window>] previous
```

## Description

[Swap](swap.md) the selected visible window with the eligible hidden window whose user-visible [refnum](number.md) is the next smaller number. With no window selector, use the current window. If there is no smaller eligible hidden refnum, wrap to the highest eligible hidden refnum.

This forms a cycle through eligible hidden windows in descending numerical order. Refnums need not be consecutive. For example, from window 5 with eligible hidden windows 3 and 1, the first operation selects 3, the second selects 1, and the next wraps to 5, which became hidden during the first swap.

Candidates are drawn from the global invisible-window list across all servers, excluding windows with [window swappable](swappable.md) off or [window skip](skip.md) on. The original window becomes hidden and retains its contents and associations. The replacement keeps its own settings and server and takes the screen position and available space, adjusting output height for its status bars and toplines.

If the original was its screen's input window, the replacement takes that role. If it was EPIC's global current window, the replacement becomes current. An explicit selector can swap another visible window without changing global current selection. The replacement's notified flag and activity indication are cleared. No window is destroyed and no IRC request is sent.

This is the operation behind [swap_previous_window](../bind/swap_previous_window.md). To cycle among visible split windows instead, use [previous_window](../bind/previous_window.md). [window next](next.md) cycles hidden windows in the other direction.

## Errors and arguments

If no eligible candidate exists, EPIC reports `There are no invisible windows to swap in` and stops subsequent window operations. This can happen even when hidden windows exist but all are skipped or unswappable.

The selected target must be visible and swappable. Otherwise the swap helper reports `You can only SWAP an invisible window with a visible window.` or `Window <name-or-number> is not swappable` and leaves visibility unchanged. Candidate selection happens first, so its failure can take precedence.

There are no arguments or options specific to this operation. Subsequent words are processed as other window operations. **After a candidate has been selected, those operations target that candidate even if the swap failed.** This differs from `window next`, which returns the resulting global current window as the subsequent target.

`/window -previous` also performs the operation; a null argument is accepted for the key binding. Successful swapping normally produces no confirmation message.

## Example

```text
/window previous
/window 7 previous
```

Swap the current window, or visible window 7, with the next lower eligible hidden refnum, wrapping when necessary.

## History

This operation existed in ircII.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_previous` selects hidden candidates and returns the candidate even after a swap error; `swap_window` implements geometry, visibility, selection, and notification changes. `swap_previous_window` delegates the binding to this handler.
