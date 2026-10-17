# on dcc_raw (removed)

## Former synopsis

```text
/on [<noise>]dcc_raw "<pattern>" { <commands> }
```

## Description

The `dcc_raw` event formerly reported data and connection events for raw TCP sockets created through [connect](../functions/connect.md) or [listen](../functions/listen.md). Scripts sent data over those sockets with [dcc raw](ctcp_raw.md). Allowing default handling displayed a message.

EPIC6 has removed the DCC raw transport and this named hook. It does not generate raw-socket data, connection-completion, acceptance, or closure events through this interface. There is no default raw-socket message to customize or suppress.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Connection file descriptor returned by `connect`. |
| `$1` | Connected host, as a hostname or IP address. |
| `$2` | Event code listed below. |
| `$3-` | Event-specific data listed below. |

| Former event code | Meaning | Former `$3-` |
| --- | --- | --- |
| `c` | Connection closed. | Not provided. |
| `d` | Incoming data. | Received data. |
| `e` | Nonblocking outgoing connection completed successfully. | Remote port number. |
| `n` | A listening socket accepted a new connection. | Incoming connection port number. |

These describe the removed interface, not arguments supplied by EPIC6. The former recommendation to assign a separate nonzero hook serial number to each socket application does not make this event available again.

## Compatibility

Attempting to register `/on dcc_raw ...` is rejected with `No such ON function: DCC_RAW`. Retained script references do not restore the event or socket transport. The current `connect` and `listen` function entries return an empty string through their active placeholder branches; they do not create the former raw DCC sockets. `/dcc raw` is unavailable too.

Scripts that used this event for socket applications must provide a new transport and event-handling implementation. There is no built-in replacement hook with these fields. Do not confuse this event with [on raw_irc](../on/raw_irc.md), which concerns traffic on an IRC server connection.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `dccctl`, and related functionality.
- [source/hook.c](../../source/hook.c): the hook table omits `dcc_raw`; `find_hook` rejects unknown named events.
- [include/hook.h](../../include/hook.h): current event identifiers contain no DCC raw event.
- [source/functions.c](../../source/functions.c): `function_connect` and `function_listen` retain disabled DCC code and return empty strings in their active branches.
- [source/commands.c](../../source/commands.c): the command table has no built-in `dcc` command.
- [script/help.irc](../../script/help.irc): retains raw-socket hook references that depend on the removed interface.
