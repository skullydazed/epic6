# motd

Request an IRC server's message of the day.

## Synopsis

```text
/motd [server-or-nickname]
```

## Description

The message of the day commonly contains server information, network rules, and announcements chosen by its administrator. Servers often send it when you connect; `motd` requests it again.

With no arguments, EPIC sends `MOTD` to the server in the current command context, falling back to the primary server when there is no server context. A server name conventionally requests that server's MOTD. Some servers also accept a nickname to request the MOTD of that user's server. EPIC does not resolve nicknames locally: it forwards the supplied text unchanged after `MOTD`.

Replies arrive asynchronously through ordinary numeric handling. [on](on.md) hooks can handle or suppress the usual start, text, and end replies, commonly numerics `375`, `372`, and `376`. A server with no MOTD may return an error such as `422`. This built-in does not maintain a special MOTD cache or toggle its display; normal numeric hooks and settings control output.

## Restrictions

There are no client-side options or argument validation, and the built-in is always registered. The server determines accepted targets, content, remote-query restrictions, and rate limits. A request may produce an error or no reply; do not wait indefinitely for remote responses in a script.

Sending requires a usable connection. A closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent.

The bundled [script/motd](../../script/motd) reads a local distribution message file and records a timestamp. It is separate from this IRC server query.

## Examples

```text
/motd
/motd irc.example.net
```

Read any server rules or announcements returned before using that server.

## Source

Registration and the shared `send_comm` handler are in [source/commands.c](../../source/commands.c), with no special `motd` branch. Server selection and transmission are in [source/server.c](../../source/server.c); numeric hooks and ordinary reply display are in [source/numbers.c](../../source/numbers.c).
