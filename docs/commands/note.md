# note

Send a request to a server's historical IRC note service.

## Synopsis

```text
/note [command [+|-flags] [arguments]]
```

## Description

`note` is a historical IRC server command. EPIC provides the command as a forwarding interface; it does not implement a note service or store and deliver notes locally. Whether a server supports it, and the meaning of its subcommands, flags, and arguments, depend entirely on that server. Consult the server's documentation before relying on the service.

The built-in forwards the argument text as `NOTE arguments`, without parsing subcommands or flags. With no arguments, it sends a bare `NOTE`; any help, error, or other response comes from the server. There are no EPIC-specific options, argument defaults, or local success messages. A server without this extension may reject the request as an unknown command.

The request uses the current server context, falling back to the primary server when there is no current context. It needs a usable connection. Normal outgoing server [encoding](encoding.md), line-length limits, and the `send_to_server` hook apply. EPIC does not split a long request into multiple notes. See [on](on.md).

## Example

```text
/note
```

This sends a bare request; it does not guarantee that the server will display service help.

## Source

The always-registered command and generic `send_comm` handler are in [source/commands.c](../../source/commands.c). Connection selection, encoding, line limits, and transmission are in [source/server.c](../../source/server.c).
