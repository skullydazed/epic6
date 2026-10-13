# $onchannel()

## Synopsis

```text
$onchannel()
$onchannel(<channel>)
$onchannel(<nickname> <channel>)
```

## Technical

Provides two actions: returning a channel's cached member list, or testing whether a nickname is cached on a channel. [$chanusers()](chanusers.md) invokes the same handler without a name-dependent branch.

### First action: list channel members

The function first tries its first argument as a channel name. Missing input, an empty argument, or the literal `*` selects the current window's current channel name. This need not be the channel that triggered an [/on](../commands/on.md) event. Channel-list lookup uses the current execution server (`from_server`), with primary-server fallback when that context has no server.

If that channel has a nonempty cached nickname list, the list is returned and later arguments are ignored. Names are space-separated, sorted by the channel's nickname-table case mapping, and have no privilege prefixes. [$channel()](channel.md) supplies privilege annotations instead.

### Second action: test cached membership

If the first lookup produces no list and another argument remains, the function treats the first word as a nickname and the second as a channel. It returns `1` when that nickname is cached on that channel, or `0` otherwise. Further arguments are ignored. A second channel argument of `*` uses the current window's channel name.

The action is chosen by **successful list lookup**, not simply by whether the first word looks like a channel name. Thus a failed channel-list query with a second argument can become a membership test, and a first word that identifies a tracked channel with members returns its list even when a second argument was supplied.

Both actions use normal function word parsing, honoring [/xdebug dword](../commands/xdebug.md). There is no explicit server-number argument. Channel and nickname comparisons use the selected channel's server case mapping.

## Practical

Use the one-channel form to inspect members, or the nickname/channel form to test local membership. In a hook, pass the event channel explicitly. The function reads local cache records and sends no IRC request; normally EPIC tracks members only on channels it has joined.

Before `channel_sync`, the cache can be incomplete. Server privacy or anonymous-channel behavior can also limit supplied membership information. A missing list or a `0` result is not an independent confirmation that the person is absent. [/names](../commands/names.md) requests membership information asynchronously.

## Returns

First action: a cached nickname list, or the empty string when no list is available and no second argument remains. Second action: `1` for cached membership or `0` for no matching record. The function does not change the channel cache or privileges.

## Examples

```text
$onchannel(#example)         request the cached member list on #example
$onchannel(*)               request the current window's channel member list
$onchannel()                same channel default as *
$onchannel(Alice #example)   ordinarily test cached membership for Alice
```

A member list might be `Alice Bob Carol`. The membership form returns `1` or `0`, subject to the action-selection rule above.

## History

The member-list action first appeared in the “+3” client (post-ircII, pre-EPIC). The equivalent ircII `chanusers()` appeared around ircII 2.6 and was added to EPIC1. The nickname/channel action appeared around ircII 2.3.24 and was added around EPIC1.

Registration and `function_onchannel()`: [source/functions.c](../../source/functions.c). Member-list creation, channel defaults, membership lookup, and nickname ordering: [source/names.c](../../source/names.c). Function word parsing: [include/words.h](../../include/words.h).
