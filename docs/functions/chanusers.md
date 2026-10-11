# chanusers function

## Synopsis

```text
$chanusers([channel])
$chanusers(nickname channel)
```

## Technical

`chanusers()` is a synonym for [`onchannel()`](onchannel.md), provided for ircII compatibility. Both names invoke the same handler without a name-dependent branch.

The usual one-channel form returns a space-separated list of cached nicknames, sorted by the channel's nickname-table case mapping, without privilege prefixes. With no arguments, or a first argument of `*`, it uses the current window's current channel name. This default can differ from the channel that triggered an [`on`](../commands/on.md) event.

Lookup uses the current execution server (`from_server`), with primary-server fallback for channel-list lookup when that context has no server. There is no explicit server or window argument. Function word parsing honors [`xdebug dword`](../commands/xdebug.md).

## Returns and restrictions

If the first argument resolves to a channel with a nonempty cached nickname list, that list is returned and later arguments are ignored. If no such list is available and no second argument remains, the result is the empty string.

If the list lookup fails and another argument remains, the shared handler instead treats the first word as a nickname and the second as a channel. It returns `1` if that nickname is cached on the channel in the current execution-server context, or `0` otherwise; further words are ignored. Selection is based on whether list lookup succeeds, rather than solely on channel-name syntax. Consequently, adding a second argument to a failed channel-list query can change an empty result into `0`.

This function sends no IRC request. EPIC normally knows members only for channels it has joined, and the cache may be incomplete before initial channel synchronization finishes. Server privacy or anonymous-channel behavior can limit available membership information. An empty list or a `0` membership result is not proof of absence when the client lacks complete information.

See [`channel()`](channel.md) for two-character privilege annotations and [`names`](../commands/names.md) for asynchronous server membership requests.

## Examples

```text
$chanusers(#example)
$chanusers(*)
$chanusers()
$chanusers(Alice #example)
```

The first three request a nickname list; the last ordinarily tests cached membership for Alice. A list might be `Alice Bob Carol`; a membership test returns `1` or `0`.

## History

This function first appeared in ircII-2.6. EPIC provides it as a compatibility synonym for `onchannel()`.

## Source

[source/functions.c](../../source/functions.c) registers both names and implements `function_onchannel()`. [source/names.c](../../source/names.c) implements `create_nick_list()`, membership lookup, default-channel selection, and nickname ordering. [include/words.h](../../include/words.h) defines argument parsing.
