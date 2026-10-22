# window check

## Synopsis

```text
/window [<window>] check
```

## Description

Check lastlog counts for every window, including hidden windows. The optional window selector does not limit the check. For each mismatch, report the window's user reference number, recounted total, and stored count. A successful check is silent.

This is a diagnostic operation; it does not repair counts or change history. It takes no arguments. `/window -check` does nothing.

## Examples

```text
/window check
```

## Source references

`windowcmd_check` in [source/window.c](../../source/window.c), with `recount_window_lastlog` in [source/lastlog.c](../../source/lastlog.c).
