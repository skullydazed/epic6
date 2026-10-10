# ping

Ask another IRC client to echo a timestamp so you can estimate round-trip delay.

## Synopsis

```text
/ping nickname
/ping channel
```

## Description

IRC messages can take time to reach someone and return. `ping` sends a specially crafted [CTCP ping](../ctcp/ping.md) message asking the recipient to echo its timestamp. A returned timestamp can be compared with the current time to estimate the round-trip delay to that client, including network and client processing time. It does not measure physical distance or just one-way latency.

The command uses [ctcp](ctcp.md), normally sending a CTCP `ping` request through IRC `PRIVMSG`. Its payload contains the current wall-clock time as integer seconds and microseconds. A channel request can reach multiple clients and produce multiple replies; use a nickname when you only need one client's response.

Not every client responds, and server policy or recipient configuration can block requests. A changed or fabricated timestamp, clock changes, or delayed handling can make the result inaccurate. No reply or accurate measurement is guaranteed. Avoid frequent channel-wide requests, which add traffic to both directions.

## Arguments and context

Supply exactly one target token. `*` selects the current window's channel, becoming a sink when no channel exists. Comma-separated destinations follow the CTCP sending layer's handling. There are no options or default recipient.

The implementation appends `PING` and the timestamp to the entire supplied argument string before passing it to the CTCP parser. Additional words are not ignored: they can change the parsed CTCP command and payload. A missing target does not print ping usage; the parser instead treats the inserted word `PING` as a destination. Do not omit the target. The intermediate string has room for only 62 bytes before its terminator, so long target lists can truncate or remove the intended command or timestamp. Prefer short explicit targets.

During incoming IRC private-message processing, including message/public and CTCP-request hooks, the CTCP layer switches to `NOTICE` reply mode. The command is therefore unsuitable for starting a new timing request there. During incoming notice processing it refuses sending with `You may not use the CTCP command from a NOTICE!`. See [on](on.md).

IRC targets require a usable connection. The command is always registered, returns no result value, and provides no ordinary outgoing message echo. The `send_ctcp` hook observes the send; server transmission hooks and limits also apply.

## Replies and server ping

The C command sends the timestamp but does not itself calculate elapsed time. The bundled [ctcp script](../../script/ctcp), loaded by [builtins](../../script/builtins), supplies a ping request echo handler and a reply handler that formats valid nonnegative elapsed time as `number seconds`. Incoming replies use the `ctcp_reply` hook and its default reporting. Custom scripts can change or remove this behavior.

This command is separate from the IRC server's `PING` keepalive command. Incoming server `PING` messages raise the `ping` hook and ordinarily receive a `PONG` response; they do not use this timestamp request mechanism.

## Examples

Estimate round-trip delay to a friend:

```text
/ping booya
```

Ask clients on a channel to respond:

```text
/ping #blah
```

## Source

Registration, `pingcmd`, and the shared CTCP command parser are in [source/commands.c](../../source/commands.c). Framing and CTCP reply processing are in [source/ctcp.c](../../source/ctcp.c), timing handlers in [script/ctcp](../../script/ctcp), and server keepalive handling in [source/parse.c](../../source/parse.c).
