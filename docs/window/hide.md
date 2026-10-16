# window hide

## Synopsis

```text
/window [<window>] hide
```

## Description

Remove the selected window from its screen. With no window selector, hide the current window. The window continues to exist and function normally: its server, channels, queries, stored output, and other settings are retained. Bring it back later with [window show](show.md).

The remaining visible windows expand to fill the vacated space, normally giving the additional rows to non-fixed windows first. Hiding does not close the screen, delete the window, leave its channels, or send an IRC request. The hidden window becomes the first entry in the invisible-window list.

If the hidden window was its screen's input window, EPIC selects that screen's previous input window when it is still available, otherwise the first remaining visible window. After removal, EPIC resets its global current window to the input window of the screen that most recently received input. On the usual single-screen setup, this is the replacement input window. Subsequent operations in the same `/window` command use that current window, rather than the hidden window.

## Restrictions

- A window that is already hidden reports `Window <name-or-number> is already invisible`.
- A window with [window swappable](swappable.md) off reports `Window <name-or-number> can't be invisible`.
- EPIC reports `You can't hide the last window.` when the screen has at most one visible window remaining after excluding windows with **both** [window fixed](fixed.md) and [window skip](skip.md) on. This check applies even if the window you want to hide itself has both flags. Thus hiding can be refused when several windows are visible.

These failures leave visibility unchanged. The last-window check uses the combination of `fixed` and `skip`, not `fixed` alone.

There are no arguments or options specific to `hide`. Additional words are processed as subsequent window operations. Even after a failed ordinary hide, subsequent operations use EPIC's current window. `/window -hide` is a no-op. Successful hiding normally produces no confirmation message.

## Examples

```text
/window hide
/window 3 hide
```

Hide the current window or window 3, subject to the restrictions above. To make window 3 visible again:

```text
/window show 3
```

## History

This operation existed in ircII.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_hide`, `hide_window`, and `count_fixed_windows` implement selection and restrictions; `remove_window_from_screen`, `add_to_invisible_list`, `set_screens_current_window`, `make_window_current_by_refnum`, and `recalculate_windows` implement visibility, replacement selection, and geometry changes.
