# restart

Ask the current IRC server to restart itself.

## Synopsis

```text
/restart [server-specific-arguments]
```

## Description

`restart` requests a restart of the IRC server daemon. An accepted restart can disconnect clients and reload server configuration when the daemon starts again. Exact behavior, availability, and any supported arguments depend on the server implementation and its administrative policy.

EPIC sends a bare `RESTART` with no arguments, or forwards the entire argument text as `RESTART arguments`. It does not parse or validate server-specific parameters, check operator privileges, or restart the EPIC client. There are no EPIC-specific options or argument defaults, and the built-in is always registered.

## Restrictions and output

Servers normally limit this operation to authorized IRC operators, sometimes requiring additional administrative privileges. See [oper](oper.md). A server can reject the request or disable the command entirely. Consult its administrative documentation before relying on a particular restart effect.

The request requires a usable connection and uses the current server context, falling back to the primary server when no context exists. Normal outgoing server [encoding](encoding.md), line limits, and the `send_to_server` [on](on.md) hook apply.

The command returns no value and produces no local success confirmation. Notices, errors, or connection loss are handled through the normal incoming-message and server-connection paths. Sending the request does not prove the daemon restarted, and this command itself does not arrange automatic client reconnection. See [reconnect](reconnect.md).

## Example

Request a restart if you are authorized and the server supports it:

```text
/restart
```

For configuration reloads that may avoid a full restart, see [rehash](rehash.md).

## Source

Registration and the generic `send_comm` handler are in [source/commands.c](../../source/commands.c). Connection selection, transmission, and connection-loss handling are in [source/server.c](../../source/server.c), with ordinary protocol and numeric replies in [source/parse.c](../../source/parse.c) and [source/numbers.c](../../source/numbers.c).
