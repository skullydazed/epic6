# ctcp command

## Synopsis

```text
/ctcp <target> [command [arguments]]
```

## Description

CTCP is the Client To Client Protocol. It sends specially framed messages to other clients through IRC, commonly to request information such as their version or local time. A recipient may reply if its client supports and permits the request; no reply is guaranteed.

`target` is required. It can be a nickname, channel, comma-separated target list, or a destination accepted by EPIC's [`msg`](msg.md) sending layer. `*` means the current window's channel; with no current channel it becomes the sink target `0`, so nothing is sent. It does not substitute the current query nickname.

The CTCP command defaults to `version` when omitted. A supplied command token is uppercased for transmission; command names in documentation are written lowercase. The remaining argument text is sent as that command's payload. There are no command-line options or validation that the recipient supports the command. With no target, EPIC prints a usage message.

The sender surrounds the command and optional payload with CTCP delimiter bytes (control-A). User-originated requests use IRC `PRIVMSG`; replies use `NOTICE`. Payload text is recoded for the destination before framing, and ordinary send hooks/local message echoes are disabled for this send. A `send_ctcp` hook observes the outgoing message, but its return value does not cancel transmission.

## Request and reply context

Outside IRC message handling, this command sends a request. While processing an IRC PRIVMSG, including message/public and CTCP request hooks, it sends a reply instead. This choice uses the current server's message-processing state; it is not limited to `ctcp_request` and `ctcp` hooks.

During NOTICE processing, the command refuses to send and reports `You may not use the CTCP command from a NOTICE!`. There is no flag to override this or explicitly choose request/reply mode.

The command is available in every build. IRC destinations need a usable connection; client errors, server restrictions, and recipient policy can prevent delivery. Long messages are bounded by the sender's IRC-sized buffer and can be truncated while preserving a closing CTCP delimiter. Use ordinary ASCII CTCP command names; fancy non-ASCII names are not supported reliably by the framing/recoding path.

## Receiving CTCP messages

You can customize incoming CTCP handling with scripts and [`ctcpctl`](../functions/ctcpctl.md). The `ctcp_request` hook runs before default request handling and can suppress it. The `ctcp` hook controls normal request reporting after processing. Replies are offered to `ctcp_reply`. See [`on`](on.md).

The bundled [ctcp script](../../script/ctcp) provides handlers and descriptions for common CTCP commands. To ask what a client advertises as supported, send it `clientinfo`, for example `/ctcp nickname clientinfo`. The response reflects that client's configuration, not a guarantee that every request will succeed.

The bundled [builtins script](../../script/builtins) supplies `verbose_ctcp`, defaulting to on, through a `ctcp` hook. Turning it off suppresses normal request notices through that script; it is not a C setting of this command.

## Limitations

The current sending path does not implement special `=nickname` DCC CHAT routing or automatic encrypted conversations. Such a target or feature must not be assumed to work merely because older CTCP interfaces supported it.

Server policy and receiving clients determine which messages are delivered and answered. Channel requests can reach many clients; use an individual nickname when a single reply is sufficient.

## Examples

To request another client's version, using the explicit command or its default:

```text
/ctcp nickname version
/ctcp nickname
```

To ask a client to echo a payload, if it supports `echo`:

```text
/ctcp nickname echo send this back
```

To query supported requests:

```text
/ctcp nickname clientinfo
```

## Related CTCP topics

Common command topics include [action](../ctcp/action.md), [clientinfo](../ctcp/clientinfo.md), [echo](../ctcp/echo.md), [finger](../ctcp/finger.md), [ping](../ctcp/ping.md), [time](../ctcp/time.md), [userinfo](../ctcp/userinfo.md), [utc](../ctcp/utc.md), and [version](../ctcp/version.md). Available handlers depend on scripts and client configuration.

## Source

See [source/commands.c](../../source/commands.c) for `ctcpcmd()` and destination handling in `send_text()`. [source/ctcp.c](../../source/ctcp.c) implements framing, recoding, hooks, and incoming processing. [source/parse.c](../../source/parse.c) manages PRIVMSG/NOTICE context, and [source/vars.c](../../source/vars.c) registers the current C settings.
