# set status_query

## Synopsis

```text
/set status_query [<text>]
/set -status_query
```

Default: ` (Query: %Q)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the query field's format rather than ending a query.

## Description

Sets the status-bar format for a window's active query target. If the window has no selected query target, the field is empty. It reflects local query selection rather than proof that the target is online or responding.

The field is represented by `%Q` in [status_format](status_format.md), or a per-window format. The first `%Q` inside `status_query` is replaced with the selected target before insertion into the outer status line. Other status percent fields are not recursively interpreted there. Omitting `%Q` from the wrapper can show a fixed label while a query is selected.

The outer format must include `%Q`. Each window supplies its own selected query target; this field has no current-window restriction under [show_status_all](show_status_all.md). It does not automatically list every nickname bound to a window.

## Effects and expansion

Changing this text rebuilds status formats and requests updates. It does not start or end a [query](../commands/query.md), change message routing, or send an IRC request. Use the query command or window query controls to change the actual target.

Ordinary command parsing can expand dollar expressions while assigning the setting. If [status_does_expandos](status_does_expandos.md) is enabled, the assembled status line can expand dollar expressions afterward. The shared wrapper helper escapes dollar signs and backslashes in the inserted target for that later pass.

## Example

Customize the active-query label:

```text
/set status_query Querying %Q
```

For target `alice`, this supplies `Querying alice` where the outer format includes `%Q`. Hide the field with `/set -status_query` without changing the query itself.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default wrapper.
- [source/status.c](../../source/status.c): `%Q` mapping, wrapper compilation, `status_query_nick`, and shared escaping.
- [source/window.c](../../source/window.c): `get_window_equery` returns the window's selected query target.
