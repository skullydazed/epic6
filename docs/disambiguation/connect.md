# Connect

Do you mean:

- The [connect command](../commands/connect.md), `/connect <server> <port> [initiating-server]`, which sends an IRC server-to-server connection request? It does not connect your client to IRC. Accepted syntax, operator permissions, routing, and success depend on the server.
- The [connect function](../functions/connect.md), `$connect(...)`, whose former raw-connection behavior is removed? The name remains registered as a compatibility placeholder that always returns an empty string; it opens no connection and ignores its arguments.

To connect EPIC itself to an IRC server, use the [server command](../commands/server.md). For retrying a client connection, see [reconnect](../commands/reconnect.md) and [server states](../special/server_state.md).

The command and function are separate interfaces despite sharing a name. Neither the server-link request nor the placeholder function returns a usable client connection identifier.

## Source references

- `source/commands.c`: `connect` uses the generic protocol-command sender; `server` has a separate handler.
- `source/functions.c`: registered `connect()` placeholder and disabled former implementation.
- `source/server.c`: client IRC connection handling.
