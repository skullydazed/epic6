# on server_established

## Synopsis

```text
/on [<noise>]server_established "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when a connection is ready to begin IRC registration. For a plain connection this follows successful TCP connection establishment; for a TLS connection it follows completion of the TLS connection path, rather than the initial TCP connection alone.

This differs from [on connect](connect.md), which runs after the server accepts IRC registration with welcome numeric `001`, not `003`. At `server_established`, registration has not yet succeeded and a subsequent authentication, nickname, or connection error can still prevent login.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches configured hostname and port together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Configured server hostname or address used for the connection. |
| `$1` | Configured server port number. |

The first field is not necessarily the resolved peer IP address or the name the server will later report in its welcome. No server refnum is included. The hook's server context is temporarily set to the server beginning registration.

## Default behavior and timing

There is no built-in display for this event. Before invoking it, EPIC changes the server state to `registering`. After the hook returns, the registration routine sends a configured password when present, starts capability negotiation with `CAP LS 302`, sends `USER`, and requests the selected nickname.

The caller ignores the hook's default-handling return value. A suppressing hook does not cancel registration or suppress those later registration sends. Merely handling this event does not mean the server has accepted your nickname or credentials. Use `connect` for work that requires accepted registration, and [on channel_sync](channel_sync.md) when it requires initial channel state.

The hook itself sends no additional request. Any commands issued by its handler are script behavior and occur before the normal registration sequence continues; server acceptance depends on protocol ordering and policy. This event can run again when a later connection attempt reaches the same stage.

## Example

Display the configured destination as registration begins:

```text
on -server_established "*" (host, port) {
    echo Beginning IRC registration with $host on port $port;
};
```

## Source references

- [source/server.c](../../source/server.c): `server_io` completes plain or TLS connection setup before invoking `server_register`; `server_register` changes state, selects the server context, invokes the hook, and then sends registration commands; `get_server_name` returns the configured host.
- [source/numbers.c](../../source/numbers.c): numeric `001` invokes the later accepted-registration path.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching.
