# set status_format1

## Synopsis

```text
/set status_format1 [<text>]
/set -status_format1
```

With no text, [set](../commands/set.md) displays the format. Unsetting removes its text, not the allocated status row.

## Description

When [window double](../window/double.md) is on, this is the top line of the two-line status bar. The bottom line uses [status_format2](status_format2.md). With double status off, the window uses [status_format](status_format.md) instead; assigning `status_format1` does not enable double status.

In other respects it uses the same syntax and rendering rules as `status_format`: case-sensitive percent fields, numeric maps, wrapper settings, optional dollar expansion, right alignment, and repeated fill. See that page for the field list and limits, including the maximum of 40 recognized fields per line. Each status row is formatted separately.

The compiled default begins with a reverse-video control character followed by:

```text
%T [%R] %*%=%@%N%#%S%{1}H%H%B%Q%A%C%+%I%O%M%F%L %U 
```

The retired `%L` field inserts nothing in EPIC6. Startup scripts can modify this default. Custom text is not automatically made reverse-video.

## Window overrides and updates

A defined [window status_format1](../window/status_format1.md) overrides this global default for that window. Changing the global setting recompiles status formats and requests updates, but preserves defined per-window overrides. Shared `status_*` wrapper settings still control their corresponding fields.

Field visibility follows the window's state, not which of the two rows contains the field. For example, [show_status_all](show_status_all.md) still governs current-only fields, and `%T` still requires [clock](clock.md) on. Dollar expressions evaluated by [status_does_expandos](status_does_expandos.md) use the displayed window's context. Changing this format sends no IRC request.

## Example

```text
/set status_format1 [%R] %N%C%+%Q%A %>%T
/set status_format2 %W %U %X %Y %Z 
/window double on
```

This configures global defaults and enables two status rows for the selected window. Existing per-window overrides can prevent these global formats from being used there.

## History

Double-status support was ported by archon from ircII to EPIC3.003.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, status rebuild callback, and default format.
- [source/status.c](../../source/status.c): `compile_status` handles global and window-specific formats; `make_status` chooses format 1 for the top row of a double status bar.
- [source/window.c](../../source/window.c): `windowcmd_double` changes the number of status rows.
