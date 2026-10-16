# window lastlog_level

## Synopsis

```text
/window [<window>] lastlog_level [<levels>]
```

## Description

Choose which output levels count as lastlog entries for the selected window and are eligible for display by [lastlog](../commands/lastlog.md). With no window selector, use the current window. With no level argument, display the current setting.

This uses the same level names as [window level](level.md), but does not route output between windows or claim levels from other windows. It changes the selected window's lastlog selection for future output. Existing entries keep the eligibility assigned when they were added; this operation neither deletes them nor rebuilds scrollback.

Unselected output is not simply discarded: EPIC also stores entries outside the mask for display reconstruction, but they do not count toward [window lastlog](lastlog.md)'s maximum and ordinary lastlog retrieval skips them. They can still appear in scrollback. This setting therefore does not control which output appears on screen or constitute a complete memory-retention filter.

## Level list

Supply one comma-delimited list, such as `msg,notice`. Level names are case-insensitive and accept prefixes. Use full names when a prefix could match more than one level. The list replaces the old selection, starting from an empty mask:

- `all` selects all levels.
- `none` or `-all` clears the selection.
- A level name adds that level; `-<level>` removes it from the selection being built, as in `all,-joins,-parts`.

A leading `+` is not an incremental-update option here. To exclude one level while retaining all others, explicitly start with `all`. Only one whitespace-delimited argument is consumed, so use commas between levels; following words are treated as subsequent window operations.

Unsupported levels produce a warning and a list of valid levels. Recognized parts still take effect, so a list containing only unsupported names leaves the selection empty. Queries and changes report `Lastlog level is <levels>`. `/window -lastlog_level` clears the selection to none and reports it, rather than acting as a query.

## Defaults

New windows copy [set lastlog_level](../set/lastlog_level.md), whose compiled default is all. Changing that `/set` value also changes the current window's selection; it does not overwrite every existing window's individual selection. `window lastlog_level` itself does not change the default for future windows. It works on invisible windows and sends no IRC request.

## Examples

```text
/window lastlog_level msg,notice
/window 3 lastlog_level all,-joins,-parts
/window 3 lastlog_level
```

Make future private messages and notices eligible for lastlog retrieval in the current window; select all levels except joins and parts in window 3; then report window 3's selection.

## Source references

- [source/window.c](../../source/window.c): the command table maps `lastlog_level` to `windowcmd_lastlog_mask`; that handler, `new_window`, and `set_window_lastlog_mask` implement arguments, defaults, and assignment.
- [source/levels.c](../../source/levels.c): `str_to_mask` builds replacement masks and `standard_level_warning` reports unsupported names.
- [source/lastlog.c](../../source/lastlog.c): `add_to_lastlog` records per-entry eligibility; lastlog filtering skips ineligible entries; `reconstitute_scrollback` can retain them for display. `set_lastlog_mask` updates the default and current window.
- [include/config.h](../../include/config.h): defines the compiled lastlog-level default.
