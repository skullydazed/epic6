# window toplines

## Synopsis

```text
/window [<window>] toplines [<lines>]
```

## Description

Reserve `<lines>` non-scrolling rows at the top of the selected window for its [topline text](topline.md). With no window selector, use the current window. Valid counts are 0 through 9; new windows default to zero.

The first `<lines>` stored toplines are displayed in order. You can change any of the nine text slots with `window topline`, even if it is not currently displayed. Reducing the count hides those rows without deleting their text. A count of zero removes all reserved topline rows.

Toplines are useful for topic bars or window information that should remain above scrolling output. They are static stored text: this operation does not populate a topic automatically or arrange periodic updates.

## Space and display effects

Increasing the count normally takes rows from the same window's scrolling output area; decreasing it returns those rows. Status-bar counts are unchanged. If the calculation would make the output area negative, EPIC clamps it to zero and recalculates the visible screen, which can affect other windows or hide windows when space is insufficient. This is not an operation that refuses every request exceeding currently free output rows.

For a visible window, EPIC recalculates screen positions and requests body and status redraws. Normal output-view adjustment can occur as the output height changes, including [window scrolladj](scrolladj.md) behavior when rows are returned. An invisible window's stored geometry can also be changed without showing it.

Stored scrollback and topline text are not cleared. Input selection normally stays unchanged, except for any selection repair caused by screen recalculation hiding windows. No IRC request is sent.

## Queries and parsing

Without a numeric argument, report `Window saved lines is <count>`. A value below zero or above 9 reports `Window saved lines must be < 10 for now.` and leaves the count unchanged. Successful changes normally produce no confirmation message.

Parsing uses a decimal numeric prefix. A following nonnumeric word is not consumed, so `toplines describe` reports the count then processes `describe`. Text after a numeric prefix is likewise left for the window dispatcher; use separated arguments for clarity. `/window -toplines` is a no-op rather than a query or reset.

## Examples

```text
/window toplines 1
/window topline 1 "Channel information"
/window toplines
/window toplines 0
```

Reserve one row, populate it, report the count, or hide all topline rows while retaining their stored text.

## History

This operation first appeared in EPIC5-0.0.3.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_toplines`, `new_window`, `recalculate_windows`, and `recalculate_window_positions` implement counts, defaults, geometry, and redraws; `resize_window_display` adjusts the normal output view.
- [source/ircaux.c](../../source/ircaux.c): `parse_number` implements decimal-prefix parsing.
- [source/screen.c](../../source/screen.c): `repaint_window_body` displays reserved topline slots above the scrolling area.
