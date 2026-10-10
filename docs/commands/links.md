# links

Request information about the IRC servers connected to your network.

## Synopsis

```text
/links [glob]
```

## Description

`links` is a server-side query for network topology. With no arguments, EPIC sends `LINKS` to the server in the current command context, falling back to the primary server when there is no server context. An optional glob conventionally filters server names, for example `*.example.net`.

EPIC forwards the entire supplied argument text unchanged after `LINKS`. It does not expand the glob, validate targets, or interpret additional arguments. Syntax beyond the simple filter, including remote-server query forms, depends on the server. There are no client-side options, and the built-in is always registered.

Replies arrive asynchronously. EPIC normally displays numeric `364` entries as the server name, its uplink, and the remaining server-supplied information, commonly a hop count and description. Numeric `365` marks the end of the list and is displayed as an ordinary server numeric. [on](on.md) numeric hooks can handle or suppress these replies.

## Restrictions

Servers may hide or simplify their topology, restrict queries to operators, rate-limit requests, or reject the command. A displayed list need not reveal the network's complete physical topology. Do not assume a reply will arrive or wait indefinitely in a script.

Sending requires a usable server connection. A closed connection normally produces a diagnostic subject to the `disconnect` hook; if there is no server record, no request is sent.

## Examples

```text
/links
/links *.example.net
```

## Source

Registration and the shared `send_comm` handler are in [source/commands.c](../../source/commands.c); there is no special `links` branch. Server selection and transmission are in [source/server.c](../../source/server.c). Numeric hooks and `364`/`365` formatting are in [source/numbers.c](../../source/numbers.c).
