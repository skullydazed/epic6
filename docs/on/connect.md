# on connect

## Synopsis

```text
/on [<noise>]connect "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC has successfully registered with an IRC server, upon processing its welcome numeric `001`. It can run again after a successful reconnection. A connection or registration attempt that fails before the welcome does not trigger this event.

This differs from [on server_established](server_established.md), which runs earlier when the connection is ready to begin IRC registration, before EPIC sends its registration messages. `connect` indicates accepted IRC registration, rather than merely an established network connection. It does not guarantee that all subsequent server information, service authentication, or channel synchronization has finished.

Use the standard [on](../commands/on.md) matching and noise controls. The pattern matches the three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Configured server hostname used for the connection. |
| `$1` | Server's configured port number. |
| `$2` | Server name reported as the sender of the welcome message. |

The configured hostname and reported server name can differ. No server refnum is included in these fields; the hook executes in the context of the server whose welcome is being processed.

## Default behavior and timing

There is no built-in output for this event. Suppression does not undo registration, close the connection, or suppress the welcome numeric's separate display.

Before invoking the hook, EPIC records the accepted nickname and reported server name, restores user modes, requests its own userhost, and handles any pending default-channel join and stored away message. Those requests may still be awaiting replies when the hook runs. The server is already considered registered, although its internal state remains `syncing` during the hook and changes to `active` afterward. Further channel checks and an initial `ison` request follow the hook.

A handler can send normal IRC commands at this point, subject to server policy. Use [on channel_sync](channel_sync.md) when a task specifically needs the initial cache for a joined channel.

## Example

Display where the client has connected:

```text
on ^connect * (ourname, port, itsname) {
    xecho -b Connected to $ourname ($itsname) on port $port;
};
```

## Source references

- [source/numbers.c](../../source/numbers.c): numeric `001` invokes `server_is_registered` before the numeric's normal display processing.
- [source/server.c](../../source/server.c): `server_is_registered` updates connection state and invokes `connect`; `server_register` invokes the earlier `server_established` hook; `get_server_name` and `get_server_port` provide configured connection values.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard hook matching and suppression.
