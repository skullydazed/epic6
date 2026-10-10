# connect command

## Synopsis

```text
/connect <new-server> <port> [existing-server]
/connect <new-server>
```

The second form is accepted only by servers that supply a default port. EPIC itself does not supply one.

## Description

`connect` requests an IRC server-to-server connection, adding or reconnecting a server to the IRC network. It does not establish your client's connection to an IRC server; use [`server`](server.md) for that.

The request normally asks your current server to connect to `new-server` on the specified port. With `existing-server`, it asks that server to initiate the connection instead. The port refers to the destination connection, not a port on the initiating server. Routing, accepted syntax, default ports, and server-name wildcard support depend on the server implementation. EPIC does not expand or resolve server-name masks itself.

This is a pass-through command. The built-in sends `CONNECT` followed by all supplied text, or bare `CONNECT` if no text is provided. It has no client-side options or argument validation, and does not check operator privileges. Missing or invalid parameters are handled by the server.

Replies and errors arrive asynchronously through the normal IRC message handlers. Numeric replies can be handled or suppressed by [`on`](on.md) hooks; the command does not wait for the link to succeed or return a result.

## Examples

To request that your current server connect to a configured peer:

```text
/connect irc.foo.com 6667
```

To ask `irc.bar.com` to initiate a connection to `irc.foo.com` on destination port 31337:

```text
/connect irc.foo.com 31337 irc.bar.com
```

On a server that permits an omitted port:

```text
/connect irc.foo.com
```

## Restrictions

This is normally an IRC-operator command. The initiating server must authorize the request, and both servers need compatible link configuration and credentials. Exact privilege and configuration requirements depend on the IRC server software; traditional C: and N: configuration lines are not a universal requirement.

A forwarded request does not guarantee a successful connection. Errors may indicate insufficient privileges, missing parameters, an unknown server, or link configuration problems. Server-name masks may be unsupported or select a different peer than expected; explicit names are clearer.

The command is available in every build and needs an active client connection to send its request. EPIC uses the current command's server context, falling back to the primary server when no context is set. A closed connection normally reports that you are not connected, unless a `disconnect` hook suppresses the message. With no usable server record, no request is sent.

## Source

See [source/commands.c](../../source/commands.c) for the `connect` registration and `send_comm()`; the shared handler has no special `connect` branch. [source/server.c](../../source/server.c) implements server selection and connection checks in `send_to_server()` and `vsend_to_aserver_with_payload()`. [source/numbers.c](../../source/numbers.c) implements numeric hooks and default reply display. The conventional protocol parameters are described in [RFC 2812, section 3.4.7](https://www.rfc-editor.org/rfc/rfc2812#section-3.4.7); individual servers can differ.
