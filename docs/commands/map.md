# map

Request a hierarchical map of the IRC network from the server.

## Synopsis

```text
/map [server-specific arguments]
```

## Description

On servers supporting `MAP`, the command commonly displays a tree-shaped view of network topology from the responding server's perspective. The server supplies both the topology and its textual layout; EPIC does not build the tree locally.

With no arguments, EPIC sends `MAP`. Any supplied text is forwarded unchanged after the command, without validation or client-side options. The request uses the server in the current command context, falling back to the primary server when there is no server context.

Replies arrive asynchronously and receive normal server-message handling. Numeric replies can be handled or suppressed using [on](on.md) hooks and ordinary numeric display settings. There is no dedicated map renderer in the client.

## Restrictions

`MAP` is a server extension, historically available on Undernet-style servers. Support, accepted arguments, access restrictions, and how much topology is exposed depend on the server. It may reject the command, restrict it to operators, hide parts of the network, or provide no reply. A returned map need not disclose the full physical network.

The built-in is always registered, which does not guarantee server support. Sending requires a usable connection: a closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent. Do not wait indefinitely for a reply in a script.

This is an IRC query, separate from script facilities that map or transform lists. See [fe](fe.md) for its list-rewriting form, or [links](links.md) for another topology query.

## Example

```text
/map
```

## Source

Registration and the shared `send_comm` handler are in [source/commands.c](../../source/commands.c), with no special `map` branch. Server selection and transmission are in [source/server.c](../../source/server.c); numeric hooks and ordinary reply display are in [source/numbers.c](../../source/numbers.c).
