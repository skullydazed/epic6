# info

Request information about IRC server software, and optionally display EPIC's own credits.

## Synopsis

```text
/info [server]
```

## Description

`info` asks an IRC server for information about its software, commonly including contributors and other credits. Without arguments, EPIC first displays local information about ircII and EPIC contributors, copyright notices, and the project contact address. It then sends an `INFO` request to the server in the current command context, falling back to the primary server when there is no server context.

With an argument, EPIC omits its local credits and forwards the supplied text after `INFO`. A server name conventionally queries that remote server. The built-in does not validate or resolve the target, and has no client-side options; additional text is interpreted by the server.

Server replies arrive asynchronously as numeric messages and can be handled with [on](on.md) hooks. Their content and accepted target forms depend on the server. Remote servers may use different software, restrict or rate-limit queries, return an error, or provide no reply. Do not wait indefinitely for a remote response in a script.

## Restrictions

The built-in is always registered. Sending the request requires a usable server connection, but displaying the local credits does not. If the chosen connection is closed, the sending layer normally reports that you are not connected; a `disconnect` hook can suppress that diagnostic. If no server record is available, no request is sent.

This command is distinct from the `info` function, which queries client information rather than sending this IRC request.

## Examples

Show local credits and query the current server:

```text
/info
```

Request information from a remote server, if the network permits it:

```text
/info irc.example.net
```

## Source

Registration and the local-credit versus argument branch are in [source/commands.c](../../source/commands.c). Server selection and connection checks are in [source/server.c](../../source/server.c); numeric hooks and normal reply display are in [source/numbers.c](../../source/numbers.c).
