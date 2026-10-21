# Floodinfo: removed incoming-flood query

The former `floodinfo()` function queried records maintained by the built-in incoming flood detector. It accepted `*` or quoted record filters and returned a sequence of quoted seven-word records. Those records described userhost, channel, message level, server, hit count, duration, and rate; numerical filters selected minimum or maximum thresholds.

EPIC6 has removed that detector and its tracking table. `floodinfo()` is absent from the current function registry, and no bundled implementation replaces it. There is no current record list, threshold-filter syntax, or recursive “feed results back as filters” contract under this name.

The former examples correlating join/part records to issue bans do not work without an actual replacement tracker. An empty or missing result from an unavailable interface is not evidence that a sender is harmless or that no flood occurred.

See [flood control: removed incoming detector](special_flood_control.md) for the retired settings and event. The supplied [floodprot script](../scripts/floodprot.md) regulates outgoing activity; it does not populate the old incoming-flood table or restore `floodinfo()`.

Scripts can implement specific counters using [on](../commands/on.md) events, but their grouping, timing, thresholds, and actions must be defined explicitly. Local display suppression does not stop network traffic or replace the server's flood policy.

## History and source

The function first appeared in EPIC4-1.1.8.

- `source/functions.c`: current registry omits `floodinfo`.
- `source/hook.c`: current event registry omits the former flood event.
- `UPDATES` and `KNOWNBUGS`: removal of built-in flood control.
- `script/floodprot`: separate outgoing-traffic script.
