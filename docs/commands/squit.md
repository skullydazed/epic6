# squit

## Synopsis

```text
/squit <server-or-mask> [<comment>]
```

## Description

`squit` asks the IRC server to disconnect a server link. IRC operators commonly use it with [connect](connect.md) when changing network routing. Disconnecting a link can split servers and their users from the rest of the network; it does not shut down the server process.

The target identifies an IRC network server, rather than an EPIC server-list refnum. Some servers permit wildcard masks, but matching and routing are server-dependent. A mask need not affect every matching server. Prefer an explicit server name when you need a particular link.

The first argument is the target, parsed as one word with double-quote support. The remaining text is the comment. With a nonempty comment, EPIC sends `SQUIT target :comment`; with no comment, it sends `SQUIT target`. The conventional IRC protocol expects a comment, so the server may reject the latter form. EPIC adds the trailing-parameter colon itself; do not add one to the comment unless it is intended as part of its text.

## Restrictions and output

This request normally requires IRC operator privileges. The server determines authorization, permitted targets, mask behavior, and comment requirements. EPIC does not check privileges or validate the target before sending. Server errors and the resulting network changes arrive asynchronously. There is no local success confirmation or script return value.

Omitting the target prints `Usage: /SQUIT <server> [<reason>]` and sends nothing. There are no client-side options or default target. The built-in does not directly close EPIC's connection, change its server list, or move its windows; use [disconnect](disconnect.md) for a client connection. An accepted network request can nevertheless cause connection loss or users to disappear as the server reports the split.

Sending uses the current server context, falling back to the primary server when there is no current context. A closed selected connection normally produces a diagnostic through the `disconnect` hook; with no server record, nothing is sent. The entire request uses the server's outbound encoding, normal outgoing mangling, line-length truncation, and `on send_to_server` handling. There is no target-specific comment recoding in this handler.

## Examples

```text
/squit irc.example.net Re-routing this link
/squit *.example.net Re-routing this link
```

Both require privileges accepted by your server. The second additionally requires supported mask syntax; which link it selects depends on the server.

## Source

Registration and `squitcmd()` are in [source/commands.c](../../source/commands.c). Quoted argument parsing is in [source/ircaux.c](../../source/ircaux.c), and server selection and outgoing handling are in [source/server.c](../../source/server.c). The conventional server-link request is described in [RFC 2812, section 3.1.8](https://www.rfc-editor.org/rfc/rfc2812#section-3.1.8).
