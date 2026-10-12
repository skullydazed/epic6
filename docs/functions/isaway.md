# isaway function

## Synopsis

`$isaway()`

`$isaway(<server refnum>)`

## Technical

Returns whether EPIC has a stored away message for your own user on the selected server. With no argument, it uses the current execution context's server (`from_server`). An explicit `-1` also selects that context's server when available. It does not query another nickname or send an IRC request.

The optional refnum is the first integer function argument; extra arguments are ignored. Base-detecting conversion accepts decimal, hexadecimal, and leading-zero octal. Nonnumeric text converts to refnum zero rather than resolving a server name.

An explicit `-2` checks whether any currently registered server has a stored away message. This same aggregate behavior applies when the current context's server is `-2`. Other invalid server refnums return zero.

The result reflects the locally stored message, not the separate server-confirmed away flag. [away](../commands/away.md) updates the message immediately, including for a known server that is not currently registered, and clearing the message makes the result zero. Server replies `305` and `306` update a separate status field without changing what this function tests. A disconnected server can therefore still return one for an explicit refnum if its message remains stored.

## Practical

Use this to change an alias's behavior according to your locally requested away state. Do not treat it as confirmation that the server accepted the request or as a connection-status test.

## Returns

`1` when a stored away message exists, otherwise `0`. The aggregate `-2` form considers only registered servers. No diagnostic or separate error return is produced; invalid selection is indistinguishable from not being away.

## Examples

| Expression | Meaning |
| --- | --- |
| `$isaway()` | Local away-message state for the current server context. |
| `$isaway(3)` | Local away-message state for server refnum three, or `0` if invalid. |
| `$isaway(-2)` | Whether any registered server has a stored away message. |

Each returns `0` or `1` according to current client state.

## Source

Registration, refnum parsing, and stored-message test: [source/functions.c](../../source/functions.c). Message storage, aggregate selection, and special refnums: [source/server.c](../../source/server.c) and [include/server.h](../../include/server.h). Separate confirmation flags: [source/numbers.c](../../source/numbers.c).
