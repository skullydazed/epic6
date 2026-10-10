# lusers

Request summary counts of users and other IRC network resources.

## Synopsis

```text
/lusers [server-mask [remote-server]]
```

## Description

`lusers`, short for “list users,” asks the server for summary information. Replies commonly describe visible and invisible users, operators, channels, and servers. Depending on the server, they may also include local and network user counts and historical maximum counts. EPIC does not calculate these statistics itself.

A server mask conventionally restricts the summary to matching servers. An optional remote-server argument conventionally directs the query to another server. Support and interpretation of both arguments depend on the network. EPIC forwards the entire supplied text unchanged after `LUSERS`; it does not expand masks, validate arguments, or resolve the remote server locally.

Without arguments, EPIC sends `LUSERS` to the server in the current command context, falling back to the primary server when there is no server context. There are no client-side options, and the built-in is always registered.

Replies arrive asynchronously and use ordinary numeric display. [on](on.md) hooks for the individual reply numbers or the general numeric event can handle or suppress them. Display prefixes follow normal numeric settings such as `show_numerics` and `banner`.

## Restrictions

Exact count definitions, which statistics are exposed, and whether invisible users are reported separately are server-dependent. Networks may restrict or rate-limit local or remote queries, return errors, or provide no reply. Do not wait indefinitely for a response in a script.

A usable server connection is required to send the request. A closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent.

## Examples

```text
/lusers
/lusers *.example.net
```

The second example requests a masked summary only if your server supports that form.

## Source

Registration and the shared `send_comm` handler are in [source/commands.c](../../source/commands.c), with no special `lusers` branch. Server selection and transmission are in [source/server.c](../../source/server.c). Numeric hooks, default reply display, and prefixes are in [source/numbers.c](../../source/numbers.c); related settings are registered in [source/vars.c](../../source/vars.c).
