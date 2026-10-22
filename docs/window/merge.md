# window merge

## Synopsis

```text
/window [<source-window>] merge <destination-window>
```

## Description

Attempt to merge the selected source window into another window on the same server. The destination is a window reference, such as a number or name. Retained lastlog records and channel associations move to the destination, and history movement requests scrollback rebuilds. Window-domain timers referencing the source's internal reference are retargeted.

The handler then attempts to [kill](kill.md) the source, makes the destination current, and uses it for subsequent operations. Destruction can fail, including when the source is unkillable; earlier transfers are not rolled back. The source's window log is closed if the source is destroyed. The destination does not inherit the source's private settings or logfile.

The implementation has material limitations: it dereferences the source target list without checking for an empty list, and transfers only its first entry. An empty source target list can crash the client; additional query/nick targets are not reliably preserved. The helper for retargeting separate [log](../commands/log.md) objects is disabled, so those references are not migrated. Do not rely on this operation to preserve every conversation or log association.

Use a distinct destination; the handler does not reject self-merges. A different-server destination is rejected. `/window -merge` does nothing.

## Examples

For distinct windows 3 and 4 on the same server, with at least one query/nick target in window 3, attempt the transfer:

```text
/window 3 merge 4
```

## Source references

`windowcmd_merge` in [source/window.c](../../source/window.c); `move_all_lastlog` in [source/lastlog.c](../../source/lastlog.c); `channels_merge_windows` in [source/names.c](../../source/names.c); `timers_merge_windows` in [source/timer.c](../../source/timer.c); `logfiles_merge_windows` in [source/logfiles.c](../../source/logfiles.c).
