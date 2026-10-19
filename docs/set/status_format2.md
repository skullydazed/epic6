# set status_format2

## Synopsis

```text
/set status_format2 [<text>]
/set -status_format2
```

With no text, [set](../commands/set.md) displays the format. Unsetting removes its text, not the allocated status row.

## Description

When [window double](../window/double.md) is on, this is the bottom line of the two-line status bar. The top line uses [status_format1](status_format1.md). With double status off, the window uses [status_format](status_format.md) instead; assigning `status_format2` does not enable double status.

All normal `status_format` syntax and rendering rules apply: case-sensitive percent fields, numeric maps, wrapper settings, optional dollar expansion, alignment, and repeated fill. See that page for the complete field list and limits, including the maximum of 40 recognized fields per line. Each row is formatted separately, so `%>` right-aligns content within this row alone.

The compiled default begins with a reverse-video control character followed by:

```text
%W %X %Y %Z 
```

`%W` is the current-window marker; `%X`, `%Y`, and `%Z` insert `status_user1`, `status_user2`, and `status_user3`. Their normal visibility conditions still apply. Startup scripts can change this default, and custom formats are not automatically made reverse-video.

## Window overrides and updates

A defined [window status_format2](../window/status_format2.md) overrides this global default for that window. Changing the global setting recompiles status formats and requests updates, while preserving defined per-window overrides. Setting it does not modify the top-row or single-row format.

[Show_status_all](show_status_all.md) governs current-only fields in the same way on either row. [Status_does_expandos](status_does_expandos.md), if enabled, expands dollar expressions in the assembled line using the displayed window's context. Changing the format sends no IRC request and does not itself provide data for empty fields.

## Example

```text
/set status_format1 [%R] %N%C%+%Q%A %>%T
/set status_format2 %W %U %X %Y %Z 
/window double on
```

This configures the global defaults and enables two status rows for the selected window. Existing per-window overrides can prevent these global formats from being used there. Turn double status off to return to the independent single-row `status_format`.

## History

Double-status support was ported by archon from ircII to EPIC3.003.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, status rebuild callback, and default format.
- [source/status.c](../../source/status.c): `compile_status` selects global or window overrides; `make_status` chooses format 2 for the lower double-status row.
- [source/window.c](../../source/window.c): `windowcmd_double` selects one or two status rows.
