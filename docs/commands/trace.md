# trace command

## Synopsis

```text
/trace [target]
```

## Description

`trace` asks your IRC server for information about the route to a specified server and the connections on that server. Traditionally, intermediate servers report that the request passed through them, showing the path from your server to the destination. With no target, the request concerns the server you are connected to.

The destination may report its server connections, services, operators, and users. What you can see depends on the server implementation and your privileges; ordinary users may receive a limited report, while operators may see more. A complete list of connections is not guaranteed.

The conventional target is a server name. Some servers also accept a server mask or nickname; a nickname target requests information about that user. These forms and the traditional route reporting are described in [RFC 2812, section 3.4.8](https://www.rfc-editor.org/rfc/rfc2812#section-3.4.8), but the connected network determines what it supports.

EPIC sends `TRACE` followed by all supplied argument text to the current server. It does not validate targets, expand masks itself, or interpret client server reference numbers. There are no client-side options. Extra arguments are forwarded for the server to accept or reject.

Replies arrive asynchronously as IRC numerics. EPIC uses its ordinary numeric display rather than constructing a local topology report. Numeric-specific [`on`](on.md) hooks and the general numeric hook can handle or suppress that output. This command does not return a function value. For a local script call stack, use [`call`](call.md).

## Restrictions and privacy

An IRC connection is required to send the request. The server may reject an unknown target, require operator privileges, limit the visible connections, rate-limit requests, or restrict remote tracing. Operator status on one server does not guarantee a complete report from another.

Do not assume the request is private or that invisible user mode prevents a user from appearing in an operator's report. Whether requests are logged or announced to operators, and which users appear, depends on server policy. EPIC does not provide those privacy guarantees.

## Examples

```text
/trace
/trace irc.example.net
```

The first requests information from your connected server. The second requests a trace to `irc.example.net`, if your network supports and permits it; replace the name with an actual server on your network.

## Source

[source/commands.c](../../source/commands.c) registers `trace` with `send_comm()`, which forwards the command and arguments. [source/server.c](../../source/server.c) implements server selection and sending. [source/numbers.c](../../source/numbers.c) handles numeric hooks and the generic reply display.
