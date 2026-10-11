# chops function

## Synopsis

```text
$chops([channel])
```

## Technical

`chops()` returns a space-separated list of nicknames cached as channel operators on one channel, without an `@` prefix. The list follows the channel's sorted nickname-table order. Half-operator or voice status alone does not qualify a nickname for this list.

If the channel argument is omitted, empty, or the literal `*`, the current window's current channel name is used. This is not necessarily the channel that triggered an [`on`](../commands/on.md) event. Lookup uses the current execution server (`from_server`), with primary-server fallback when that context has no server; the default channel name still comes from the current window.

Only the first argument word is used; later words are ignored. There is no server-number argument. Argument parsing honors [`xdebug dword`](../commands/xdebug.md), and channel lookup and nickname ordering use the server's case mapping.

This is a local cache query and sends no IRC request. You normally must be on the channel for EPIC to track its members. Before initial synchronization completes, the member list and operator flags can be incomplete. Even after synchronization, the result reflects only information supplied by the server and processed by the client, rather than an independent check of current privileges.

## Practical

Use the returned list when formatting operator information or selecting recipients. [`fe`](../commands/fe.md) can iterate over the nicknames in manageable groups, and [`sar()`](sar.md) can replace spaces with commas for commands that accept comma-separated targets. Follow the network's message limits when using the list to send messages.

Pass an explicit event channel in a handler instead of relying on `*` or the omitted argument. See [`channel()`](channel.md) for operator, half-operator, and voice annotations, [`chanusers()`](chanusers.md) for all cached nicknames, and [`nochops()`](nochops.md) for nicknames not cached as operators.

## Returns

The cached operator nickname list, or the empty string if no operators are recorded, no matching channel is tracked, or no default channel is available. These cases are not distinguished by the result. The function does not output the list to the screen or change privileges.

## Examples

```text
$chops(#foo)    returns cached operators on #foo
$chops(*)       returns cached operators on the current channel
$chops()        returns cached operators on the current channel
```

If Alice and Carol are the cached operators, a result might be `Alice Carol`.

## History

This function first appeared in “+3” (post-ircII, pre-EPIC). Support for the default argument and `*` first appeared in “+4”.

## Source

[source/functions.c](../../source/functions.c) registers `chops` and implements `function_chops()`. [source/names.c](../../source/names.c) implements `create_chops_list()`, channel defaults, nickname ordering, and operator-flag updates. [include/words.h](../../include/words.h) defines argument parsing.
