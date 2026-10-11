# chansyncing function

## Synopsis

```text
$chansyncing(channel server-description)
```

## Technical

`chansyncing()` tests whether EPIC's tracked channel record is marked as undergoing initial synchronization. Both arguments are required: the channel comes first and the server description second. Additional words are ignored. There is no omitted-argument default to the current channel or server.

The server description is resolved against existing records, accepting a server reference number or matching server description; it does not add or connect a server. An explicit channel of `*` selects the current window's current channel name and looks it up on the selected server. Channel comparison uses the server's case mapping. Argument parsing honors [`xdebug dword`](../commands/xdebug.md).

## Practical

When EPIC processes its own join, it creates or resets the channel record and marks it as synchronizing. It requests channel modes and may request WHO information before completing synchronization. The flag is cleared before [`on channel_sync`](../commands/on.md) is dispatched, so a call from that completion hook normally returns `0`.

Completion can occur without a full WHO member-cache refresh, including anonymous-channel handling or the configured maximum cached channel size. Therefore `0` means the synchronization flag is clear, not that every possible piece of server information is available. This function only reads local state and sends no request.

## Returns and restrictions

| Result | Meaning |
| --- | --- |
| `1` | A tracked channel exists and its synchronization flag is set |
| `0` | No matching record exists, or the flag is clear |
| Empty string | A required argument is missing |

A failed server-description lookup returns the no-server sentinel internally, which the channel helper interprets as the primary server. An unknown server description can therefore query a channel on the primary server instead of returning an error. Use a verified server reference number when server selection matters. A zero result does not distinguish an untracked channel from a synchronized one.

## Examples

```text
$chansyncing(#example 0)
$chansyncing(* 0)
```

The first checks `#example` on server 0. The second checks the current window's channel name on server 0. Either returns `1` while the matching channel record is marked as synchronizing, otherwise `0`.

## Source

[source/functions.c](../../source/functions.c) registers `chansyncing` and implements `function_channelsyncing()`. [source/names.c](../../source/names.c) implements the synchronization flag, lookup, and completion hook. [source/parse.c](../../source/parse.c) begins synchronization on joins, [source/numbers.c](../../source/numbers.c) handles mode/WHO completion, and [source/server.c](../../source/server.c) resolves server descriptions.
