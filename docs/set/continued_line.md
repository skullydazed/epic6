# set continued_line

## Synopsis

```text
/set continued_line [<text>]
/set -continued_line
```

Default: `+`. With no text, [set](../commands/set.md) displays the current value. Text may contain spaces; `/set -continued_line` unsets the marker and is treated as an empty prefix during wrapping.

## Description

Sets the text prepended to the second and subsequent display rows when a long output line wraps to fit a window. It provides a visual indication that the row continues the preceding message rather than starting a new one. It is not prepended to the first row; see [first_line](first_line.md) for that separate prefix.

The marker occupies display space, leaving less room for continued message text. Keep it short, particularly in narrow windows. It is processed through display normalization and mangling rather than evaluated as an expando template each time a row wraps. Normal command parsing can still expand text when the setting is assigned.

## Indentation

The marker can be combined with [indent](indent.md). Normal window output uses that window's [window indent](../window/indent.md) flag. With indentation enabled, EPIC fits the continuation marker to the calculated indentation width, padding with spaces or truncating it as needed. The indentation width is capped at one third of the window's display width; when there is no useful first-word indentation point, that cap is used.

With indentation disabled, the configured marker is used without that indentation padding. Unsetting the marker therefore removes its visible text, but indentation can still add spaces if enabled for the window.

## Scope and effects

This affects local display wrapping, not the original IRC message, event-hook arguments, or normal log and lastlog text recorded before wrapping. It does not insert the marker into messages sent to a server. Dumb-mode output goes directly to standard output and does not use this full-screen wrapping path.

The setting has no immediate redraw or scrollback-rebuild callback. It applies when display rows are subsequently prepared; already prepared scrollback rows need not change immediately. A later scrollback rebuild uses the current wrapping settings.

## Example

Use a visible marker without indentation in the current window:

```text
/set continued_line >
/window indent off
```

Long output lines then use `>` at the start of their continuation rows. Restore the default marker with `/set continued_line +`.

## Source references

- [source/screen.c](../../source/screen.c): `prepare_display` reads the marker, combines it with indentation, normalizes it, and prefixes continued rows; `add_to_window` records logs before wrapping.
- [source/window.c](../../source/window.c): `windowcmd_indent` controls per-window indentation; scrollback rebuilds prepare display rows again.
- [source/vars.c](../../source/vars.c): string registration with no immediate callback and standard unset handling.
- [include/config.h](../../include/config.h): default marker `+`.
