# time

## Synopsis

```text
/time [<server-or-nickname>]
```

## Description

`time` asks an IRC server for its local time. Without a target, the receiving server normally answers. A server name requests that server's time; where the server supports nickname targets, a nickname requests the time from the server that user is using. Target masks and remote-query restrictions are server-dependent.

EPIC forwards `TIME` and the complete argument text, or bare `TIME` when no arguments are supplied. It does not resolve nicknames, interpret options, validate targets, or calculate a local answer. Some servers restrict remote requests or reject unsupported targets, so a reply is not guaranteed.

The [time function](../functions/time.md), `$time()`, instead returns the local client's Unix timestamp. A [ctcp](ctcp.md) `TIME` request asks another client for its time; `/time nickname` does not send CTCP to that user. There is no C built-in `date` command in the current registration table.

## Output and restrictions

The conventional response is numeric 391. EPIC displays it through ordinary numeric formatting, subject to [on](on.md) `391` and `on numeric` handlers. The server supplies the date, time, timezone information, and format; EPIC does not convert it to the client's timezone. Errors also arrive through normal server-reply handling. The command has no script return value and does not set the local clock.

Sending requires a usable connection in the current server context, or the primary server when there is no current context. A closed selected connection normally produces a diagnostic through the `disconnect` hook; with no server record, nothing is sent. Normal outgoing encoding and `on send_to_server` handling apply. The command is always registered.

## Examples

```text
/time
/time irc.example.net
/time JoeBob
```

These request the receiving server's time, a named server's time, and the time of `JoeBob`'s server where nickname routing is supported.

## Source

Registration and `send_comm()` are in [source/commands.c](../../source/commands.c); the shared handler has no special `time` branch. Sending is in [source/server.c](../../source/server.c), and numeric hooks and display are in [source/numbers.c](../../source/numbers.c). The separate local function is in [source/functions.c](../../source/functions.c). Conventional server-query behavior is described in [RFC 2812, section 3.4.6](https://www.rfc-editor.org/rfc/rfc2812#section-3.4.6).
