# on server_status

## Synopsis

```text
/on [<noise>]server_status "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC assigns a server connection state through its state setter. States track asynchronous connection work as well as registration and cleanup. The setter does not require the new state to differ from the old one, so an event can report the same value in both fields.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches server refnum, old state, and new state together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum. |
| `$1` | Previous state name, or `UNKNOWN` for an invalid recorded value. |
| `$2` | Newly assigned state name. |

State names are emitted in uppercase. The current names and broad meanings are:

| State | Meaning |
| --- | --- |
| `CREATED` | Server record being constructed; initial assignment bypasses this hook. |
| `RECONNECT` | Eligible for connection, waiting for connection-driving logic. |
| `POLICY` | Connection-policy stage where scripts can adjust the server description. |
| `DNS` | Resolving connection addresses. |
| `CONNECTING` | Nonblocking TCP connection in progress. |
| `SSL_CONNECTING` | TLS negotiation in progress. |
| `REGISTERING` | IRC registration or protocol negotiation in progress. |
| `SYNCING` | Registration accepted; initial server setup in progress. |
| `ACTIVE` | Registered session's initial setup routine has completed. |
| `EOF` | Unregistered/EOF state preceding further cleanup. |
| `ERROR` | Connection error requiring handling or cleanup. |
| `CLOSING` | Close routine has begun cleanup. |
| `CLOSED` | Connection descriptor closed and cleanup completed. |
| `DELETED` | Server record being removed. |

These are implementation states rather than a promise of one fixed transition sequence. `EOF` is also assigned by explicit unregistration during normal closure; it does not always mean the server independently closed the socket. `ACTIVE` does not guarantee that every channel is synchronized or every external service has authenticated you.

## Ordering and default behavior

The setter updates the stored state first, invokes [on server_state](server_state.md), then invokes `server_status` with the same captured old and new names, and finally requests status updates. Queries in this hook normally see the newly assigned state. A preceding handler can itself cause another state assignment, so the captured transition arguments remain the reliable description of this invocation.

There is no built-in output. The setter ignores both hooks' default-handling return values: suppressing one does not undo the assignment, skip the other event, or prevent subsequent connection work. Scripts should avoid repeatedly assigning states from a handler without a terminating condition; this hook has no nonrecursion flag.

For a transition to `CLOSING`, the state assignment and its events occur before the close routine destroys channel and nickname state. [On server_lost](server_lost.md) occurs later on eligible hard-close paths, before descriptor closure, and can observe a different state. The transition itself is not a guarantee that the underlying socket is usable.

## Example

Observe connection-state assignments:

```text
on -server_status "*" (server, oldstate, newstate) {
    echo Server $server: $oldstate -> $newstate;
};
```

## History

This hook first appeared in EPIC5-0.0.2.

## Source references

- [source/server.c](../../source/server.c): `set_server_state` assigns state and invokes `server_state` then `server_status`; `server_states` supplies names; initialization, registration, unregistration, and closure establish transition behavior.
- [include/server.h](../../include/server.h): defines current state identifiers.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching without a nonrecursion flag for this event.
