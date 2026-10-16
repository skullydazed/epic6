# window scrollback

## Synopsis

```text
/window [<window>] scrollback [<rows>]
```

## Description

Set the normal size limit for the selected window's scrollback display buffer. With no window selector, use the current window. The size is measured in physical display rows after wrapping, unlike the original output entries counted by [window lastlog](lastlog.md). The buffer also counts its blank end-of-output marker, so a limit of N does not promise exactly N retained text rows.

When normal output is added, EPIC trims the oldest display rows until the buffer fits. Changing the limit itself does not immediately trim or rebuild the buffer. Lowering it takes effect on later trimming; increasing it does not recover rows already discarded.

Trimming is suspended while a scrollback view is active, or while the held view has output beyond a full visible page. The buffer can then exceed this limit and use additional memory. Merely enabling hold mode without a backlog does not suspend trimming. After views catch up, the next normal output-and-trim pass can enforce the limit again.

The display buffer is distinct from lastlog. A [scrollback rebuild](rebuild_scrollback.md), such as after a column-width change, reconstructs it from retained lastlog entries, so a large scrollback limit alone does not guarantee preservation of old output. Explicit flushing can also discard rows independently.

## Defaults and arguments

New windows copy [set scrollback](../set/scrollback.md), whose compiled default is 256. Changing that `/set` value updates every existing window's limit, overwriting individual settings. A direct `window scrollback` changes only the selected window.

The requested value is raised to at least twice the window's current output height. Zero and negative integers are accepted but select that minimum rather than disabling scrollback. Growing a window also raises an undersized limit to twice its new output height; shrinking does not automatically lower the limit.

Without an argument, report the current limit. A query or change reports `Window scrollback size set to <number>`. Nonnumeric input reports `SCROLLBACK: You must specify a number`, leaves the limit unchanged, and still prints the current-limit report. Use ordinary decimal integers; numeric conversion also interprets a leading zero as octal.

If any words follow the operation, the first is consumed as its size argument. Additional words are processed as subsequent window operations. `/window -scrollback` is a no-op, not a query. The operation works for invisible windows without showing them or changing input selection, and sends no IRC request.

## Examples

```text
/window scrollback 1000
/window 3 scrollback 500
/window 3 scrollback
```

Set the current window's display-buffer limit to 1000 rows, request 500 for window 3 subject to its height minimum, or report window 3's limit.

## History

This operation first appeared in EPIC4pre1.028.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_scrollback`, `new_window`, `set_scrollback_size`, `resize_window_display`, and `trim_scrollback` implement defaults, minimums, global changes, and trimming conditions.
- [source/screen.c](../../source/screen.c): `add_to_window` wraps output and invokes trimming after adding its rows.
- [include/config.h](../../include/config.h): defines the compiled scrollback default.
