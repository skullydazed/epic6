# userhost

Return the current message sender's userhost, or look up nicknames in EPIC's channel caches.

## Synopsis

```irc
$userhost()
$userhost(<nickname list>)
```

## Technical

With no arguments, the function returns the `user@host` portion of the sender prefix of the IRC message currently being processed. This is available in message hooks such as `/on msg`, `/on public`, `/on join`, and `/on part`, and in aliases or functions called from those hooks. A message without that portion of its prefix yields an empty string. Ordinary use outside message processing normally returns an empty string. `/on raw_irc` runs before the new sender information is extracted, so it cannot use this form to obtain the incoming line's sender address.

With arguments, the function treats them as nicknames and looks for each userhost in the channel caches for the current execution server. It returns a space-separated list in the same order as the nicknames. An unknown nickname or a nickname whose address has not been cached contributes the literal `<UNKNOWN>@<UNKNOWN>`.

Nickname arguments use normal function word parsing: double-quoted grouping is honored when `/xdebug dword` is enabled. There is no separate server argument, channel-lookup form, or option. A channel name is treated as a nickname; it does not trigger host resolution.

This lookup does not block or launch a server query. It can find addresses only for users recorded in channels EPIC tracks on that server. A known nickname can still lack a cached address, particularly before channel synchronization finishes.

## Practical

The no-argument form supplies sender information that older hook argument lists do not include. Use the [userhost command](../commands/userhost.md) to request addresses from the server when the cache cannot answer. A cached address is server-supplied userhost text; it need not be a numeric IP address.

## Returns

With no arguments, the current sender's userhost, or an empty string. With nickname arguments, one address or unknown-address placeholder per nickname.

## Examples

```irc
on ^part "*" {
    echo *** $0 ($userhost()) left channel $1
}
echo Nuke!$userhost(Nuke)
$userhost(Alice Bob) /* Alice's cached address, then Bob's, or placeholders. */
```

## History

The function originally appeared in ircII 2.1.5. Nickname arguments were added in EPIC4pre0.027.

## Source

Registered and implemented as `function_userhost` in [source/functions.c](../../source/functions.c). Cache lookup uses `fetch_userhost` in [source/names.c](../../source/names.c). Sender context is established by `BreakArgs` and normally restored after dispatch by `parse_server` in [source/parse.c](../../source/parse.c).
