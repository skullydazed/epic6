# rping

Request a server-to-server ping measurement through IRC.

## Synopsis

```text
/rping server [server-specific-arguments]
```

## Description

`rping` requests timing information for another server across the IRC network. It serves a similar diagnostic purpose to [ping](ping.md), but sends an IRC server command rather than a [CTCP](ctcp.md) request to a client. Timing describes network and processing delay, not physical distance.

EPIC forwards all supplied text as `RPING arguments`. It does not validate the target, append a timestamp, or parse server-specific parameters. With no arguments it sends a bare `RPING`, leaving any missing-parameter error to the server. There are no EPIC-specific options or defaults, and the built-in is always registered.

Support is server-dependent. Historically associated with ircu servers, this extension cannot be assumed to exist on every IRC network. Servers commonly restrict it to authorized IRC operators; actual privileges, routing syntax, and the measurement returned depend on the server. See [oper](oper.md).

## Replies and restrictions

EPIC recognizes an `RPONG` reply with parameters for your nickname, the measured server, a millisecond value, and an original-time field. Its normal display is:

```text
Pingtime replying-server - measured-server : milliseconds ms (total delay: seconds s)
```

The millisecond value is displayed as supplied by the server. The additional total delay is calculated by subtracting the integer conversion of the original-time field from the current epoch time. It is meaningful only when that field contains a compatible epoch timestamp; the sender does not automatically provide one, and this calculation does not validate it. Server variants may therefore produce inaccurate or incompatible output. Replies missing required fields follow ordinary protocol-error handling.

The command has no local success message or result value and does not guarantee a reply. Incoming replies pass through `raw_irc` [on](on.md) handling before protocol dispatch; there is no dedicated `rpong` hook in this handler.

Sending requires a usable IRC connection and uses the current server context, falling back to the primary server when no context exists. Normal outgoing server [encoding](encoding.md), line limits, and the `send_to_server` hook apply.

## Example

If your server supports this target syntax and you are authorized:

```text
/rping remote.example.net
```

Consult the server's documentation for any required additional parameters.

## Source

Registration and generic `send_comm` forwarding are in [source/commands.c](../../source/commands.c). `RPONG` dispatch and `p_rpong` reporting are in [source/parse.c](../../source/parse.c), with connection selection and transmission in [source/server.c](../../source/server.c).
