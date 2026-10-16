# window back

## Synopsis

```text
/window [<window>] back
```

## Description

Make the input screen's previous current window current again. If that window is visible, select it and update input focus on its screen, much like [window refnum](refnum.md). If it is invisible, attempt to swap it into the selected window's screen position, much like [window swap](swap.md). If the input screen has no recorded positive previous-window reference, use its top window instead, like [window goto](goto.md) `1`.

The history used is the last-window reference of the input screen, even when a window selector is supplied. The selector identifies the outgoing window for an invisible-window swap. If the destination is visible on another screen, it becomes that screen's input window; the original screen's input window remains unchanged.

Unlike [window last](last.md), `back` can bring an invisible previous window into view. It does not choose a window by numeric order or apply the `skip` selection rule. Repeated use normally toggles between the last two windows as focus changes or swaps update the saved reference.

This built-in window operation takes no arguments or options. Additional words are processed as subsequent window operations. `/window -back` is a no-op. The command sends no IRC request; normal window/status updates and the `switch_windows` hook accompany current-window changes.

## Swap limitations

The outgoing window must be visible, and both windows must permit swapping through [window swappable](swappable.md). Otherwise, the swap helper reports a diagnostic such as `Window name is not swappable` or `You can only SWAP an invisible window with a visible window.`

The handler makes the destination logically current before attempting the swap. If the swap fails, that current-window change is not rolled back, so an invisible destination can remain logically current without becoming visible.

## Example

```text
/window back
```

Return to the previous window. If it was hidden after a swap, bring it back into the current window's place when swapping is allowed.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_back`, `set_screens_current_window`, `make_window_current_by_refnum`, and `swap_window` implement history lookup, focus, swapping, diagnostics, and hook invocation; `windowcmd_last` handles the related visible-window operation.
