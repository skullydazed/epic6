# uping command

## Synopsis

```text
/uping server
```

## Description

`uping` requests a server-provided ping diagnostic, traditionally associated with ircu servers such as those used by Undernet. On a supporting server, it can help investigate network latency or IRC lag. The measurements and endpoints are determined by the server; this command does not measure Internet distance directly from your client.

EPIC forwards `UPING` followed by all supplied argument text to the current IRC server. It does not open a UDP socket, resolve the target locally, run the Unix `ping` utility, or calculate latency. There are no client-side options, target defaults, or argument validation. With no arguments, EPIC sends bare `UPING` for the server to accept or reject.

The server determines the accepted target syntax, any additional parameters, and how results are reported. Replies arrive asynchronously through normal IRC message handling; EPIC has no dedicated `uping` result formatter or function return value. Use [`ping`](ping.md) for EPIC's CTCP ping request to an IRC client.

## Restrictions

An IRC connection and server support are required. A server may reject the command as unknown, require IRC operator privileges, reject a target, or impose limits. Availability is not guaranteed by connecting to a particular network, and EPIC does not enforce an Undernet-only restriction locally. Consult the connected server's documentation for its syntax and permissions.

## Example

On a server that supports this form:

```text
/uping irc.example.net
```

Replace the name with a target accepted by that server. A successful request does not guarantee replies or a particular timing report.

## Source

[source/commands.c](../../source/commands.c) registers `uping` with `send_comm()`, which forwards the command and its arguments. [source/server.c](../../source/server.c) selects the current server and sends the IRC line. [source/numbers.c](../../source/numbers.c) and [source/parse.c](../../source/parse.c) handle incoming server responses.
