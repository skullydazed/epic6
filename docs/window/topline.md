# window topline

## Synopsis

```text
/window [<window>] topline <line> ["<text>"]
```

## Description

Set the text of a non-scrolling line reserved at the top of the selected window. With no window selector, use the current window. `<line>` is numbered from 1 through 9, from top to bottom. Use [window toplines](toplines.md) to choose how many of those lines are displayed; setting a line's text does not itself reserve screen space or make it visible.

You can change any of the nine lines even when that line or the entire window is hidden. New windows have no topline text and display zero toplines. Showing fewer lines does not erase the stored text in the remaining slots.

The text is one argument; use double quotes for spaces. `-` clears the slot. Omitting text or supplying an empty quoted string also makes that line blank, but does not remove its reserved row. To remove topline rows, change `window toplines` instead.

Displayed text is fitted to the window's column width, truncated or padded rather than wrapped into more toplines. The redraw path uses the stored text without status-expando or alias expansion; scripts must update it when dynamic information changes. It is separate from the scrolling output buffer and is not added as ordinary output to scrollback or lastlog.

## Errors and arguments

A missing line number silently does nothing. Nonnumeric input reports `Usage: /WINDOW TOPLINE <number> "<string>"`; a value outside 1 through 9 reports `/WINDOW TOPLINE number must be 1 to 9.`. The stored slots are unchanged on these failures. Use ordinary decimal line numbers; numeric conversion also treats a leading zero as octal.

Additional words after the text are processed as subsequent window operations. To blank a line and continue with another operation, explicitly supply `-` or `""`; otherwise the next word is consumed as text. `/window -topline` is a no-op.

A valid assignment or clearing requests a window-body redraw and normally produces no confirmation message. It does not change visibility, input selection, or the number of toplines, and sends no IRC request.

## Examples

```text
/window toplines 1
/window topline 1 "This stays above scrolling output"
/window topline 2 "Stored, but not displayed yet"
/window topline 1 -
```

Reserve one topline, set it, store text for a currently undisplayed second line, and clear the first line's text while retaining its reserved row.

## History

This operation first appeared in EPIC5-0.0.3.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_topline`, `windowcmd_toplines`, and `new_window` implement text slots, range checks, independent row reservation, and defaults.
- [source/screen.c](../../source/screen.c): `repaint_window_body` renders stored topline text using `prepare_display_fixed_size` before the scrolling output rows.
