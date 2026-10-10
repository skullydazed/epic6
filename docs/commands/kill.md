# kill

Ask the IRC server to forcibly disconnect a client from the network.

## Synopsis

```text
/kill nickname [comment]
```

## Description

`kill` is an IRC server command used by IRC operators to eject a client. If the server accepts the request, that client's current channel memberships and associated privileges are lost. The client may reconnect afterward; a kill is not a persistent ban.

The server determines the required operator privileges, whether an operator may target local or remote users, protected users, and any required comment. EPIC forwards the request without checking those permissions. Server responses and errors arrive asynchronously.

The first whitespace-delimited argument is the target, and the remaining text is the comment. With a nonempty comment, EPIC sends `KILL target :comment`, applying the target's outbound [encoding](encoding.md) rules. Without a comment, it sends `KILL target`; many servers require a reason and may reject this form.

## Restrictions

Supply an explicit nickname. The shared C handler substitutes the current window's channel when the target is omitted or is `*`, falling back to literal `*` when no current channel exists. That generic fallback is not a useful default nickname for `kill`, and the server can reject it. There is no local usage diagnostic or nickname validation.

The command has no client-side options and does not split nickname lists. Additional target syntax, if supported, is interpreted by the server. Sending requires a usable connection in the current command context, or the primary server when there is no server context. A closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent. Outgoing text is subject to IRC line-length truncation in the sending layer.

The built-in is always registered. Its name refers to the IRC request; it does not terminate a local [exec](exec.md) process or exit EPIC.

## Example

```text
/kill nickname Disconnecting this client for policy violation
```

This requires the privileges accepted by your server.

## Source

Registration and the shared `send_2comm` handler are in [source/commands.c](../../source/commands.c), with no special `kill` branch. Target-specific comment recoding is in [source/recode.c](../../source/recode.c); connection checks, formatting, and line limits are in [source/server.c](../../source/server.c). Incoming kill messages are handled separately in [source/parse.c](../../source/parse.c).
