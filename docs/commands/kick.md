# kick

Ask the IRC server to remove a user from a channel.

## Synopsis

```text
/kick channel nickname [reason]
/kick * nickname [reason]
```

## Description

`kick` sends an IRC `KICK` request through the server in the current command context. Supply a channel explicitly, or use `*` for the current window's channel. The nickname is the next whitespace-delimited argument, and all remaining text is the optional reason. EPIC applies the channel's outbound [encoding](encoding.md) rules to that reason.

Removing a member normally requires channel-operator privileges, but the server decides the exact permissions and restrictions. Some networks protect services or particular users from kicks. Membership, privilege hierarchy, and other server policies can reject a request even when you are an operator. EPIC does not check these conditions locally or guarantee removal.

A kick revokes current membership; it is not a ban against joining again. Server responses and errors arrive asynchronously, and local membership state follows the server's messages.

## Arguments and restrictions

Both channel and nickname are required; omitting either prints usage. There are no client-side options, and the command is always registered. EPIC does not validate the channel prefix or split comma-separated channel or nickname lists. It forwards those tokens as given; multi-target support and pairing are server-dependent. Use separate requests when you need independent reasons or predictable single-target handling.

If no reason is supplied, EPIC still sends an empty trailing reason parameter. It does not substitute your nickname or a default comment locally; the server determines how that is handled.

Use `*` only when the current window has a channel. Unlike some other channel commands, this handler has no explicit check for a missing current channel before formatting the request.

Sending requires a usable server connection. The sending layer normally reports a closed connection, subject to the `disconnect` hook; when no server record exists, no request is sent. Long outgoing lines are subject to the sending layer's IRC line-length truncation.

## Examples

Remove `foobar` from `#blah` with a reason:

```text
/kick #blah foobar Please stop flooding
```

Remove `foobar` from the current channel:

```text
/kick * foobar Please follow the channel rules
```

## Source

Registration and `send_kick` are in [source/commands.c](../../source/commands.c). Reason recoding is in [source/recode.c](../../source/recode.c), and request formatting, connection checks, hooks, and line limits are in [source/server.c](../../source/server.c). Incoming kick handling is in [source/parse.c](../../source/parse.c).
