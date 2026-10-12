# $isconnected()

## Synopsis

```text
$isconnected([<server_refnum>])
```

## Technical

Tells you whether EPIC has an active IRC protocol session with the selected server. It returns `1` for the `SYNCING` and `ACTIVE` server states, after IRC registration has succeeded. An open socket alone does not qualify: connection, TLS negotiation, and registration still in progress return `0`.

This returns the same connection flag as `$serverctl(get <server_refnum> connected)`. See [serverctl](serverctl.md).

Without an argument, the function uses the current execution context's server (`from_server`), also available as `$serverctl(from_server)`. Explicit `-1` selects the same context. This is not necessarily the current window's server. Explicit `-2` returns `0`; it does not test all servers or select the primary server. Other invalid or deleted server references also return `0`.

The optional reference is the first integer function argument; additional arguments are ignored. Conversion accepts decimal, hexadecimal, and leading-zero octal. Nonnumeric text converts to zero rather than resolving a server name.

The function reads local state and sends no request. A `1` result means EPIC considers registration complete, not that a particular IRC command will succeed or that the remote server is guaranteed to respond.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | The selected server is in `SYNCING` or `ACTIVE` state. |
| `0` | The selected server reference is invalid, or its IRC session is not registered. |

## Examples

```text
if (isconnected()) {
    msg someone Hi there!
}
```

This checks the same server context that the message uses. `$isconnected(1)` checks server reference `1` without changing the context for subsequent commands; use [xeval](../commands/xeval.md) with `-s` to run commands on another server.

## Source

Registered and implemented as `function_isconnected` in [source/functions.c](../../source/functions.c). Server reference lookup, `is_server_registered`, registration state transitions, and `serverctl`'s `connected` property are in [source/server.c](../../source/server.c). Integer argument conversion is in [include/functions.h](../../include/functions.h).
