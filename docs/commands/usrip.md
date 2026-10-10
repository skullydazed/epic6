# usrip command

## Synopsis

```text
/usrip [-direct] [-count number] [-extra variable] [nickname ...] [-cmd { commands }]
/usrip -flush
```

## Description

`usrip` uses the same machinery as [`userhost`](userhost.md), but sends the legacy `USRIP` server command to request an IP address instead of a hostname. This spelling was associated with older hybrid servers. Use it when your server documents that form; [`userip`](userip.md) is the other available spelling, and neither is guaranteed to be supported by a particular network.

The server determines address disclosure, permissions, and reply format. An IPv4 dotted-quad address is not guaranteed. With no nicknames and no callback, the request concerns your own nickname. Normal output queries the server and displays returned entries as `nickname is username@address`, with operator and away annotations. Missing nicknames produce no ordinary display.

A `-cmd` request may execute synchronously from EPIC's userhost cache if every nickname is cached. This is not a separate IP cache: `$4` can contain a hostname, and cached operator and away flags are forced to `-`. Use `-direct` to request fresh server data. If any nickname is uncached, the entire list is queried asynchronously.

## Options and callbacks

| Option | Effect |
| --- | --- |
| `-cmd commands` | Execute the remaining command text, optionally braced, once per requested nickname in request order. Put nicknames and other options before it. |
| `-direct` | Bypass the userhost cache and send the request to the server. |
| `-count number` | Change nicknames per request from five. Use a positive number within the server's supported limit; it is not validated. |
| `-extra variable` | Capture the named variable's current value for callback or normal-output hook arguments. |
| `-flush` | Discard requests still waiting to be sent in the shared userhost queue; outstanding requests remain. |

Options are case-insensitive. The callback receives nickname in `$0`, operator sign in `$1` (`+` for an operator), away sign in `$2` (`+` for away), username in `$3`, returned address in `$4`, and extra data in `$5-`. A missing nickname receives `<UNKNOWN>` for username and address and `-` for both flags. It runs in its own local scope and argument context and replaces normal display and its per-entry numeric hook. See `userhost` for option abbreviations, parsing, captured data, and queue details.

With `-cmd` but no preceding nicknames, the command reports `USRIP -cmd with no nicks specified`. It has no function return value and otherwise inherits the same diagnostics and defaults as `userhost`.

## Restrictions

The built-in is always registered but silently does nothing unless the current server is registered. Its reply handling is identical to `userip`: numeric 302 enters the shared queue, while numeric 340 enters it only when the server advertises **USERIP** in 005, even though this request uses the `USRIP` spelling. Other reply formats do not complete this queue automatically.

For normal queued output, the corresponding numeric hook receives nickname, operator sign, availability sign, username, address, and extra data. Its availability sign is `-` for away and `+` for available, opposite to the callback's away sign.

Unsupported commands, missing replies, or incompatible reply formats can leave later requests waiting. Do not send raw address requests with [`quote`](quote.md), because their replies can interfere with the shared queue. Server privacy policy may hide or mask addresses even when the request succeeds.

## Example

On a server documenting compatible `USRIP` support:

```text
usrip -direct joebob -cmd {
    echo $0 has server-reported address $4
}
```

## Source

[source/who.c](../../source/who.c) implements `usripcmd()` and the `USRIP` branch of `userhostbase()`, with shared queues, caching, and callbacks. [source/numbers.c](../../source/numbers.c) implements numeric 302/340 routing. [source/commands.c](../../source/commands.c) registers the command.
