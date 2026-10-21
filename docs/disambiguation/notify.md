# Notify

The former C built-in `notify` command and `notify()` function are removed. For the current nickname-watch command, see the [notify script](../scripts/notify.md):

```text
/load notify
/notify alice
/notify -alice
```

The script defines `/notify` as a wrapper around the current server's MONITOR facility. It checks advertised support and has no ISON polling or WATCH fallback. Its watch list belongs to that server connection; server limits and replies determine the result. See the script page for list clearing, status requests, and the mixed add/remove limitation.

The script's `$notify(...)` function-call branch is an unfinished stub: it returns without supplying a value and performs no monitoring request. It does not restore the old function's list-query results. Existing scripts that call it must be updated rather than interpreting its empty result as a complete online/offline list.

Related meanings:

- [window notify](../window/notify.md) controls activity messages for hidden windows; it is unrelated to nickname presence.
- [notifywindows](../functions/notifywindows.md) reports windows with their notified flag set.
- [set notify](set_notify.md) and [notify_interval](set_notify_interval.md) describe removed built-in polling settings, not controls for the MONITOR script.

## Source references

- `script/notify`: MONITOR aliases, numeric display hooks, and unfinished function branch.
- `source/commands.c` and `source/functions.c`: current registries lack the former built-in `notify` interfaces.
- `source/window.c` and `source/screen.c`: independent window notification state.
- `KNOWNBUGS`: retirement of the old subsystem and introduction of the script replacement.
