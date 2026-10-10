# squery

## Synopsis

```text
/squery <servicename> <text>
```

## Description

`squery` sends a message to a network service that uses the IRC service protocol. On a supporting server, [servlist](servlist.md) can show available services. The service determines which requests it accepts and what responses it sends.

The first whitespace-delimited argument is the service name; all remaining text is the message. With nonempty text, EPIC sends `SQUERY servicename :text`. It applies the target's outbound [encoding](encoding.md) rules to the text and the server's encoding to the command and target. There are no locally interpreted options.

This command sends `SQUERY`, rather than `PRIVMSG`. A network's service bots may instead expect [msg](msg.md), and their presence does not establish support for the IRC service protocol. Follow the service's instructions.

## Restrictions and output

Supply an explicit service name and message. The shared C handler substitutes the current window's channel when the target is omitted or is `*`, falling back to literal `*` when there is no current channel. That fallback is not a useful service destination. With no text, EPIC sends `SQUERY target` without a trailing parameter. The client does not print a usage error or validate that the target is a service; the server may reject these requests.

Sending uses the current server context, with the primary server as a fallback when there is no current context. A closed selected connection normally produces a diagnostic through the `disconnect` hook; with no server record, nothing is sent. The normal sending layer applies outgoing mangling, IRC line-length truncation, and `on send_to_server` handling.

Unlike `msg`, this built-in does not perform normal message echo, invoke `on send_msg`, or update the last message destination. Server errors and service responses arrive asynchronously and are displayed or handled according to their received message types. There is no script return value. The command is always registered, but server and service support are required for useful results.

## Example

To ask an `irchelp` service for help about private messages:

```text
/squery irchelp help privmsg
```

This works only if that service exists and accepts this request syntax.

## Source

Registration and `send_2comm()` are in [source/commands.c](../../source/commands.c); the shared handler has no special `squery` branch. Target-specific recoding is in [source/recode.c](../../source/recode.c), and server selection, payload formatting, and outgoing handling are in [source/server.c](../../source/server.c). The conventional protocol is described in [RFC 2812, section 3.5.2](https://www.rfc-editor.org/rfc/rfc2812#section-3.5.2).
