# on ctcp_request

## Synopsis

```text
/on [<noise>]ctcp_request "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered early when EPIC receives a [CTCP](../commands/ctcp.md) request in an IRC `privmsg`, before dispatching it to a registered CTCP handler. This gives a script the opportunity to implement a response or suppress normal processing.

This differs from [on ctcp](ctcp.md), which runs later when generic processing reaches the user-notification step. Incoming CTCP replies carried in `notice` do not use this event; see [on ctcp_reply](ctcp_reply.md).

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches all four fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1` | Destination as received, normally your nickname or a channel. |
| `$2` | CTCP command name as received. |
| `$3-` | Arguments, possibly empty. |

The argument text undergoes incoming character-set conversion before the hook. The hook precedes registered handlers, including the built-in action handler, but follows CTCP extraction and parser filtering.

## Default behavior and suppression

The hook has no direct built-in output. If it allows default handling, EPIC continues to the registered CTCP handler, if any, and then to generic display unless a special handler bypasses that display. EPIC6 initially registers only the built-in action handler; other responses can be supplied by scripts.

A suppressing hook, such as a serial-zero `^ctcp_request` hook, prevents the request's registered handler and its later generic `ctcp` notification. It also causes any remaining CTCP payloads in the same IRC message to be discarded without processing. Ordinary non-CTCP text in the message remains available for normal message handling. Avoid broad suppression when other scripts depend on requests such as [DCC](../disambiguation/ctcp_chat.md) or when you want normal action display.

Not every CTCP payload reaches this hook. Empty payloads, messages with more than eight CTCP delimiter characters, destinations beginning with `$`, and `#` channels the client has not joined are discarded before request handling. Reaching generic display for an earlier payload also stops later CTCP processing in that message.

## Sending a response

Use `ctcp <sender> <type> <reply text>` inside the handler for a CTCP reply. During incoming private-message processing, the built-in `ctcp` command sends a `notice` reply rather than a new `privmsg` request. Delivery still depends on the connection and server policy.

Use proper CTCP replies instead of ordinary [msg](../commands/msg.md) responses. The built-in message sender does not automatically convert `msg` into [notice](../commands/notice.md) in this hook; an explicit `notice` remains a notice. Handling a request does not itself send any response.

## Example

Customize [version](../ctcp/version.md) replies while suppressing further handling of that request:

```text
on ^ctcp_request "% % VERSION*" (sender, recvr, type, args) {
    ctcp $sender version EPIC6 with myscript;
};
```

## Source references

- [source/ctcp.c](../../source/ctcp.c): `do_ctcp` extracts and recodes payloads, invokes this hook before handlers, and implements suppression and parser limits; `init_ctcp` registers the action handler.
- [source/parse.c](../../source/parse.c): `p_privmsg` sets private-message processing state and passes requests to `do_ctcp`.
- [source/commands.c](../../source/commands.c): `ctcpcmd` chooses reply framing in a private-message context; `send_text` selects `privmsg` or `notice` from the requested command.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
