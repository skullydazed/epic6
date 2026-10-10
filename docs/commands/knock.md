# knock

Ask an IRC channel for an invitation, when the server supports this request.

## Synopsis

```text
/knock channel [server-specific arguments]
/knock * [server-specific arguments]
```

## Description

`knock` sends a `KNOCK` request asking for an invitation to a channel. This is useful for invite-only channels, where an [invite](invite.md) may be needed before you can [join](join.md). It does not itself join the channel or guarantee an invitation.

The server decides whether `KNOCK` is supported, who receives the request, which channels permit it, and whether repeated requests are limited. Feedback and errors arrive asynchronously. Avoid repeated knocks while waiting for a response.

## Arguments and restrictions

The first whitespace-delimited argument is the channel. `*` substitutes the current window's channel; without a current channel, EPIC prints a diagnostic and sends nothing. Omitting the first argument prints usage. Other channel text is forwarded without local validation or automatic prefix insertion.

Remaining arguments are appended unchanged after the channel. There are no client-side options, and EPIC does not add a colon to group a multiword reason. If your server supports such a reason and requires a trailing parameter, include its colon explicitly, for example `/knock #private :Please invite me`.

The request uses the server in the current command context, falling back to the primary server when there is no server context. A usable connection is required: a closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent. The built-in is always registered, and registration does not imply server support. Normal outbound server encoding and IRC line-length limits apply.

## Examples

```text
/knock #private
```

Use the current channel as the target:

```text
/knock *
```

Whether knocking while already joined is meaningful or allowed is a server decision.

## Source

Registration and the shared `send_channel_com` handler are in [source/commands.c](../../source/commands.c). The handler has no special `knock` branch. Server selection, connection checks, encoding, and request transmission are in [source/server.c](../../source/server.c).
