# $numonchannel()

## Synopsis

`$numonchannel([<channel>])`

## Technical

Returns the number of member records in EPIC's local cache for the selected channel. It does not query the server or independently verify membership. Before the `channel_sync` event, the count can be incomplete; synchronization is not checked or required by this function.

If the channel argument is omitted, empty, or the literal `*`, the current window's current channel name is used. This is not necessarily the channel that triggered an [/on](../commands/on.md) event. Lookup uses the current execution server (`from_server`), with primary-server fallback when that context has no server. The default channel name still comes from the current window.

Only the first argument word is used, and later words are ignored. There is no server-number argument. Parsing honors [/xdebug dword](../commands/xdebug.md), and channel lookup uses the server's case mapping. To inspect a channel on another server, run the query in that server context rather than adding a server argument.

## Practical

Use this count to display channel size or make a script decision based on locally tracked membership. Normally EPIC tracks members for channels it has joined. A script reacting to an event should pass that event's channel explicitly instead of relying on the omitted argument.

If you use the count to calculate a channel limit or another mode change, the result is only local knowledge and the server determines whether the later mode request is permitted. [$chanusers()](chanusers.md) returns the cached member names, and [$chansyncing()](chansyncing.md) reports synchronization state separately.

## Returns

A nonnegative cached member count. Returns `0` if no matching channel is tracked, no default channel is available, or the channel has no cached members. These cases are not distinguished. No diagnostic is printed, and the function changes no state.

## Examples

```text
$numonchannel(#example)    cached member count on #example in the execution server context
$numonchannel(*)           cached member count for the current window's channel name
$numonchannel()            same channel default as *
```

If the cache contains Alice, Bob, and your own nickname, the count is `3`; it is not a count of other members only.

## History

This function first appeared in “plus 9” (post-ircII, pre-EPIC).

Registration and `function_numonchannel()`: [source/functions.c](../../source/functions.c). `number_on_channel()`, channel defaults, server fallback, and member records: [source/names.c](../../source/names.c). Argument parsing: [include/words.h](../../include/words.h).
