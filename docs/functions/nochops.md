# $nochops()

## Synopsis

`$nochops([<channel>])`

## Technical

Returns a space-separated list of nicknames **not cached as channel operators** on the selected channel. Half-operator or voice status alone does not exclude a nickname. The result contains plain nicknames without privilege prefixes and follows the channel's sorted nickname-table order.

If the channel argument is omitted, empty, or the literal `*`, the current window's current channel name is used. This is not necessarily the channel that triggered an [/on](../commands/on.md) event. Lookup uses the current execution server (`from_server`), with primary-server fallback when that context has no server; the default channel name still comes from the current window.

Only the first argument word is used; later words are ignored. There is no server-number argument. Parsing honors [/xdebug dword](../commands/xdebug.md). Channel lookup and nickname ordering use the server's case mapping.

This function reads EPIC's local member cache and sends no IRC request. You normally must be on the channel for its members to be tracked. Before `channel_sync` is raised, the member list or operator flags may be incomplete: a nickname with no cached operator flag can appear even if the server has not yet supplied its privileges. The list is not an independent confirmation of server permissions.

## Practical

Use the list to select or display members who are not recorded as operators. Pass an explicit event channel in a hook instead of relying on the omitted argument or `*`. This function only returns names; sending messages, changing modes, or kicking members requires separate commands and the appropriate server permissions.

[$chops()](chops.md) returns the cached operators, [$chanusers()](chanusers.md) returns all cached members, and [$channel()](channel.md) adds operator, half-operator, and voice annotations.

## Returns

The cached non-operator nickname list, or the empty string when no such nicknames are recorded, no matching channel is tracked, or no default channel is available. These cases are not distinguished. The source cache and channel privileges are not changed.

## Examples

```text
$nochops(#foo)    returns cached non-operators on #foo
$nochops(*)       returns cached non-operators on the current channel
$nochops()        returns cached non-operators on the current channel
```

If Alice is an operator, Bob is voiced, and Carol is a half-operator without operator status, the result includes Bob and Carol but excludes Alice.

## History

This function first appeared in “+3” (post-ircII, pre-EPIC). Support for the default argument and `*` first appeared in “+4”.

Registration and `function_nochops()`: [source/functions.c](../../source/functions.c). `create_nochops_list()`, channel defaults, nickname ordering, and operator-flag updates: [source/names.c](../../source/names.c). Argument parsing: [include/words.h](../../include/words.h).
