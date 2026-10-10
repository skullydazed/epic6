# wallchops command

## Synopsis

```text
/wallchops channel text
/wallchops * text
```

## Description

On a supporting server, `wallchops` sends a message to the channel operators of the specified channel rather than everyone on it. EPIC sends the literal `WALLCHOPS` server command; it does not select recipients locally or emulate this feature when the server lacks it.

The first whitespace-delimited argument is the target, and the remaining text is the message. Quotes are not removed from the target. `*` substitutes the current window's channel; if that window has no channel, the literal `*` is sent. With no arguments, the same channel default applies and a target-only request is sent. With a target but no message, EPIC likewise sends `WALLCHOPS target` for the server to accept or reject.

A nonempty message is sent as one trailing parameter, `WALLCHOPS target :text`, using the target's outbound [`encoding`](encoding.md) rules. There are no client-side options, channel checks, or privilege checks. The server determines support, allowed targets, who may send, and exactly which channel statuses receive the message.

On some networks, the alternative is [`msg`](msg.md) `@#channel text`, using the server's supported status-target syntax. EPIC does not automatically switch to that form. Check the network's rules rather than assuming either spelling guarantees delivery only to channel operators.

## Output and restrictions

The built-in is always registered and requires an IRC connection to send. It does not produce a normal local message echo, return a function value, or use the ordinary `msg` send-message hooks. Connection failures and server errors can produce diagnostics; any resulting messages are handled through normal IRC parsing. Server message-length limits and the normal outbound line limit apply.

## Examples

On a server supporting `WALLCHOPS`:

```text
/wallchops #channel this message is intended for channel operators
/wallchops * please check the current channel
```

On a network supporting operator status targets instead:

```text
/msg @#channel this message is intended for channel operators
```

These are channel-operator messages, not a private message to a single nickname.

## Source

[source/commands.c](../../source/commands.c) registers `wallchops` with `send_2comm()`, which selects the default target and constructs the request without a command-specific branch. [source/recode.c](../../source/recode.c) implements outbound recoding, and [source/server.c](../../source/server.c) sends the command and trailing payload.
