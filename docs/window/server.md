# window server

## Synopsis

```text
/window [<window>] server [<server-description>]
```

## Description

Associate the selected window with a server, connecting or reconnecting when necessary. With no window selector, use the current window. The server description can identify an existing server by number or describe a host and connection parameters. If no argument is supplied, report `Window <window-number> connected to server <server-number>`; this reports association, not a guarantee of an active connection.

Connection establishment is asynchronous. The operation returns before a new connection is ready, so immediately following commands must not assume registration has completed. Network reachability, TLS negotiation where configured, authentication, and nickname acceptance are determined during connection and by the server.

If changing associations, EPIC clears pending channel associations and attempts to move the window's old channels to other windows on their server. It does not migrate those channels to the new server or automatically join them there. Stored output, query/nick target entries, and local settings remain with the window. The `window_server` hook receives the window number and old/new server numbers.

Other windows keep using the old server. If none remains associated with it, normal server checking closes its connection when its `autoclose` property is enabled, as it is by default. Use [window noserv](noserv.md) to detach without selecting another server.

## Server descriptions

The traditional positional form remains supported:

```text
host[:port[:password[:nickname[:group[:type]]]]]
```

Omitted positional fields require their separating colons. Named fields avoid counting colons, for example `irc.example.org:PORT=6667:NICK=JoeBob`. Keys are case-insensitive. Once named fields begin, continue using named fields. Other supported server-description fields include `PASS`, `GROUP`, `TYPE`, `PROTO`, `VHOST`, and `CERT`; see [server](../commands/server.md) for connection configuration. TLS connection types require the client's TLS support and suitable server configuration.

The window operation consumes one whitespace-delimited argument. Server configuration also supports JSON internally, but JSON containing spaces cannot be passed as a single argument through this operation's plain word parser.

EPIC first looks for a matching existing server and uses it without updating its configuration from the supplied description. Only when lookup finds no match does this handler insert a server description. Thus specifying a nickname or another field here does not necessarily modify an existing server or an already established session.

## Connection and level effects

When moving to a server already in its connection lifecycle, EPIC can reclaim unclaimed levels from [set old_server_lastlog_level](../set/old_server_lastlog_level.md), default none. The reclaim helper checks whether levels are claimed anywhere, then enforces exclusive claims among windows on the destination server. It preserves existing claims on the moved window; see [window level](level.md).

When moving to a closed or failed server, the handler requests reconnection. Servers already connecting continue through normal server checking. Specifying the same associated server is normally a no-op; if that server is closed, it requests reconnection. This is not an unconditional reconnect command for an active server.

There are no separate operation flags. Additional words are processed as subsequent window operations on the same selected window. `/window -server` is a no-op. The operation works for invisible windows without showing them and does not change input selection. Successful reassociation normally has no confirmation message of its own; hooks and connection processing can produce output and errors.

## Examples

```text
/window server irc.example.org:6665
/window server irc.example.org:::JoeBob
/window 3 server 0
/window 3 server
```

Select or create a server description using port 6665, supply a preferred nickname using omitted positional fields, associate window 3 with configured server 0, or report window 3's association. A matched existing server is reused as described above.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_server`, `window_change_server`, `renormalize_window_levels`, and `window_check_servers` implement reassociation, notifications, level handling, asynchronous startup, and autoclose.
- [source/server.c](../../source/server.c): `serverinfo_load`, `load_cjson`, `serverdesc_lookup`, `serverdesc_upsert`, `serverinfo_matches_servref`, and `disconnectcmd` implement description parsing, matching, insertion, and reconnect requests.
- [source/names.c](../../source/names.c): `reassign_window_channels` attempts reassignment on the old server.
