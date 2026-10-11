# chanlimit function

## Synopsis

```text
$chanlimit([channel ...])
```

## Technical

`chanlimit()` returns EPIC's cached mode `+l` user limit for each supplied channel, as a space-separated list of integers in argument order. Channel names are parsed as words using the function word mode controlled by [`xdebug dword`](../commands/xdebug.md), and compared using the server's channel-name case mapping.

With no arguments, it queries the current window's current channel. An explicit `*` also selects that channel name. All lookups use the current execution server (`from_server`); if that context has no server, the channel helper uses the primary server. The current window supplies the default channel name even if the execution server is different. There is no server-number argument; a numeric token is treated as a channel name.

## Practical

Use this function to inspect limits already known to EPIC. It sends no IRC request and does not count channel members or report remaining capacity. The channel must have a record in the client's tracked channel list.

New channel records begin with a zero limit. Received mode information updates the stored limit, and removing `+l` resets it to zero. Incomplete synchronization or missed mode information can make the cache unavailable or stale. Use [`mode`](../commands/mode.md) to request server information where appropriate; responses are asynchronous and server-dependent. A zero cached value by itself does not prove that a channel has no server-side limit.

## Returns

| Result for one channel | Meaning |
| --- | --- |
| Positive integer | Stored channel user limit |
| `0` | No nonzero limit stored in the channel record |
| `-1` | No matching channel record, or no default channel available |

No-argument calls still produce one numeric result. An explicitly empty first channel argument selects the default channel, but an empty argument after a result has already been added stops processing the list. This distinction matters when `dword` mode permits empty quoted words.

## Examples

```text
$chanlimit(#example)
$chanlimit(#example #other)
$chanlimit(*)
$chanlimit()
```

If EPIC has cached limits of 25 for `#example` and zero for `#other`, the second call returns `25 0`. An untracked channel produces `-1` in its position in the result list. The last two calls query the default channel.

## Source

[source/functions.c](../../source/functions.c) registers `chanlimit` and implements its channel loop in `function_channellimit()`. [source/names.c](../../source/names.c) implements `get_channel_limit()`, default channel/server lookup, and mode-limit cache updates. [include/words.h](../../include/words.h) defines argument word parsing.
