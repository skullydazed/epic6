# servlist

## Synopsis

```text
/servlist [<name> [<type>]]
```

## Description

`servlist` asks the current IRC server for services connected to the network and visible to you. Such services may provide network functions such as nickname or channel administration. For services that use this protocol, [squery](squery.md) sends them a message.

This queries the network's IRC service facility. To display EPIC's own list of server connections, use [server](server.md) without arguments.

## Options

An optional wildcard name pattern limits the service names returned. An optional service type further restricts the results; use the types recognized by your network. With no arguments, the conventional request asks for all visible services.

EPIC forwards `SERVLIST` followed by the complete argument text, or just `SERVLIST` when no arguments are given. It does not interpret the filters, validate types, add defaults, or implement local flags. The current server context is used, with the primary server as a fallback when there is no current context.

## Output and restrictions

The server determines which services are visible and whether it supports this command. A network's ordinary service bots are not necessarily registered as IRC services, so their existence does not guarantee a `servlist` entry. An unsupported request may produce an unknown-command error; an empty result does not establish that the network has no service bots.

The conventional replies are numeric 234 for each service and 235 at the end of the list. EPIC displays them through its generic numeric handling; numeric-specific [on](on.md) handlers and `on numeric` can customize or suppress that display. The command has no script return value and maintains no local list of services.

A usable server connection is required. If the selected server exists but is closed, EPIC normally prints a connection diagnostic. If no server is available, nothing is sent. Normal outgoing encoding and `on send_to_server` handling apply.

## Examples

```text
/servlist
/servlist *help*
```

These request all visible services, then only those whose names match `*help*`, on a server supporting this protocol.

## Source

Registration and the shared `send_comm()` implementation are in [source/commands.c](../../source/commands.c). Server selection and outgoing handling are in [source/server.c](../../source/server.c). Generic reply display and hooks are in [source/numbers.c](../../source/numbers.c). The conventional service-query parameters and replies are described in [RFC 2812, section 3.5.1](https://www.rfc-editor.org/rfc/rfc2812#section-3.5.1); actual server behavior can differ.
