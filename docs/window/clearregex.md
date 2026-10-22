# window clearregex

## Synopsis

```text
/window [<window>] clearregex "<regex>"
```

## Description

Permanently remove retained lastlog records whose text matches a case-insensitive extended regular expression. Matching deletions request a scrollback rebuild. With no selector, use the current window. Saved log files are unchanged.

The expression is one argument; quote it if it contains spaces. Matching searches anywhere in each record unless the expression uses anchors such as `^` and `$`. An invalid expression reports a compilation error and deletes nothing. Omitting the argument or using `/window -clearregex` does nothing.

This changes existing history, not future output filtering. See also [clearlevel](clearlevel.md) and [lastlog](../commands/lastlog.md).

## Examples

```text
/window 3 clearregex "connection lost"
```

## Source references

`windowcmd_clearregex` in [source/window.c](../../source/window.c) and `clear_regex_from_lastlog` in [source/lastlog.c](../../source/lastlog.c).
