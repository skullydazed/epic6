# guh

## Synopsis

```text
/load guh
$whatchan(<nickname>)
$is_on(<nickname> [<nickname>...])
$is_off(<nickname> [<nickname>...])
$uh(<nickname> [<nickname>...])
/inc <variable> [<amount>]
```

## Description

This script supplies functions for querying information about other IRC users, plus an increment alias. The lookup functions use the current server context. They issue WHOIS, ISON, or USERHOST requests and use `wait for` so replies can arrive before the function returns. Waiting continues processing client events; a slow or unresponsive server can delay completion. These are script aliases, not independent C lookup functions.

The server controls the information available. Hidden channels, host cloaks, visibility restrictions, missing users, and network-specific replies affect the results. They are snapshots, not proof that a nickname will still be present when you use it. Queries need a registered connection; without one, an underlying request may silently return and the function can produce an empty or otherwise unhelpful result.

## Functions

### whatchan

`whatchan(<nickname>)` sends [whois](../commands/whois.md) for its first argument and installs a temporary suppressing numeric `319` hook. The captured value is `$1-` of that numbered reply. In the current numeric argument layout, `$0` is the reporting server and `$1` is the queried nickname, so the result includes the nickname followed by the reported channel words, not just channel names. Channel words can retain status prefixes such as `@` or `+` supplied by the server.

The handler restores the previous `319` hook list on its first activation. It does not match the requested nickname or server specifically, does not accumulate multiple `319` replies, and leaves other WHOIS output to normal handling. A concurrent WHOIS reply can therefore supply the wrong result.

If no `319` arrives, the function normally returns empty after its wait but does not restore the saved hook list. This includes users with no visible channels or failed queries. A later unrelated `319` can then trigger the leftover handler. Do not rely on this helper for isolated concurrent WHOIS queries or complete channel discovery.

### is_on and is_off

`is_on(<nicknames...>)` submits [ison](../commands/ison.md) and returns the nicknames collected from numeric `303`. With one requested nickname it returns the reported nickname when online, or empty when not reported online. It supports several nicknames, not only one. Returned spelling and visibility follow the server's response.

It temporarily removes existing installed `303` hooks, collects replies with a suppressing handler, and restores the saved list after waiting. That handler accepts every `303`, not just replies for this call, so other outstanding ISON work can contaminate the result. The function also sets the current server's `maxison` property to `0`, disabling the local outstanding-ISON limit, and does not restore the previous value.

`is_off(<nicknames...>)` calls `is_on`, then removes each returned online nickname from the original input list using `remw`. It returns the remaining names, preserving their input spelling. An empty or incomplete online result can therefore make unavailable information look like an offline result; this is not an independent confirmation that each name is offline.

### uh

`uh(<nicknames...>)` uses [userhost](../commands/userhost.md) with a command callback and returns collected `user@host` words. It does not include the nicknames or operator/away flags in its result. USERHOST may use cached information or query the server; one callback runs for each processed nickname.

Unknown users can yield `<UNKNOWN>@<UNKNOWN>` rather than being omitted. The output should not be assumed to be a list of validated IP addresses or physical hosts: servers can report cloaked hostnames. There is no DNS lookup or geographic inference in this helper.

## Increment alias

`inc <variable> [<amount>]` adds the amount to the named variable through indirect expression assignment. The amount defaults to `1`. It changes client script state, sends no IRC request, and defines no numeric function result. It is not an atomic counter shared with other clients or processes.

## Examples

```text
/load guh
/echo $is_on(Alice Bob)
/echo $is_off(Alice Bob)
/echo $uh(Alice)
/inc example_counter
/inc example_counter 5
```

The queries can wait for server replies. The final two commands increment the named script variable by one and five. Missing or malformed lookup arguments are not comprehensively validated by this script; use explicit nicknames.

## Other notes

Written by Jeremy Nelson (hop).

## Source references

- [script/guh](../../script/guh): query wrappers, temporary hooks, waits, and increment alias.
- [source/who.c](../../source/who.c): ISON request queues, `303` dispatch, USERHOST cache use, and callback fields.
- [source/numbers.c](../../source/numbers.c): generic numbered-hook fields and WHOIS `319` handling.
- [source/commands.c](../../source/commands.c) and [source/server.c](../../source/server.c): `wait for`, server round-trip waiting, and `maxison` control.
- [source/alias.c](../../source/alias.c) and [source/hook.c](../../source/hook.c): function-return scope and hook-list save/restore behavior.
