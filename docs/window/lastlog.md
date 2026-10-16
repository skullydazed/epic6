# window lastlog

## Synopsis

```text
/window [<window>] lastlog [<lines>]
```

## Description

Set the selected window's maximum lastlog size to `<lines>` entries. With no window selector, use the current window. When the counted lastlog exceeds this limit, EPIC deletes its oldest entries until it fits. This operation changes storage; use [lastlog](../commands/lastlog.md) to display or search retained output.

The count is of original output entries selected by [window lastlog_level](lastlog_level.md), not wrapped display rows. An entry wrapping across several screen rows still counts once. Entries outside that level selection are stored separately from the counted size and are removed by this trimming pass only when older than a counted entry being removed. Thus the limit is not a strict cap on every stored entry or on scrollback rows.

Changing the limit trims immediately and schedules a scrollback rebuild from the remaining lastlog data. Lowering it can permanently discard history. Increasing it cannot recover deleted entries. Saved log files are unchanged, and no IRC request is sent.

## Defaults and minimum

New windows copy [set lastlog](../set/lastlog.md), whose compiled default is 256. Changing `/set lastlog` updates all existing windows, overwriting their individual limits; `window lastlog` can then override one window again.

After setting and trimming, EPIC raises the stored limit to at least twice the window's output height. The order matters: a request smaller than that minimum trims to the requested size first, then raises the limit for future output. In particular, setting zero can discard counted lastlog entries even though the reported limit is subsequently raised. A query without an argument also applies this minimum, without trimming or scheduling a rebuild.

## Arguments and output

`<lines>` must be a nonnegative integer. A nonnumeric value reports `/WINDOW LASTLOG takes a number (you said <value>)` and stops subsequent window operations. A negative value reports `Lastlog size must be non-negative` and leaves the limit unchanged. Use ordinary decimal integers; numeric conversion also interprets a leading zero as octal.

A query or successful change reports `Lastlog size is <number>`. Additional words are processed as subsequent window operations. Unlike the ordinary no-argument query, `/window -lastlog` only reports the current stored limit and does not apply the minimum. The operation works on invisible windows without making them visible.

## Examples

```text
/window lastlog 50
/window 3 lastlog 500
/window 3 lastlog
```

Request a limit of 50 entries in the current window, set window 3's limit to 500, and report window 3's limit. The first example does not guarantee exactly 50 rows of scrollback, and its stored limit is raised if the output area is taller than 25 rows.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_lastlog`, and `rebuild_scrollback` implement defaults, trim-before-minimum ordering, reporting, and rebuilding.
- [source/lastlog.c](../../source/lastlog.c): `add_to_lastlog`, `trim_lastlog`, `set_lastlog_size`, and `reconstitute_scrollback` implement counted entries, trimming, global changes, and display reconstruction.
- [source/ircaux.c](../../source/ircaux.c): `is_number` and `my_atol` validate and convert the argument.
- [include/config.h](../../include/config.h): defines the compiled lastlog default.
