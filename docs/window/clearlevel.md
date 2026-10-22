# window clearlevel

## Synopsis

```text
/window [<window>] clearlevel <levels>
```

## Description

Permanently remove retained lastlog records of the specified output levels from the selected window. Matching deletions request a scrollback rebuild. With no selector, use the current window. Saved log files are unchanged.

Supply the levels as one comma-separated word, such as `MSG,NOTICE`. `ALL` selects all levels; a following negative level excludes it, as in `ALL,-PUBLIC`. Unrecognized levels produce a warning, but recognized portions are still used. See [lastlog_level](lastlog_level.md) for level selection.

Omitting the argument or using `/window -clearlevel` does nothing. This removes existing records; it does not change which levels are retained for future output.

## Examples

```text
/window 3 clearlevel MSG,NOTICE
```

## Source references

`windowcmd_clearlevel` in [source/window.c](../../source/window.c), `str_to_mask` in [source/levels.c](../../source/levels.c), and `clear_level_from_lastlog` in [source/lastlog.c](../../source/lastlog.c).
