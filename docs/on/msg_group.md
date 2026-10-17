# on msg_group

## Synopsis

```text
/on [<noise>]msg_group "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Similar to [on msg](msg.md), but handles incoming messages whose destination is neither your nickname nor a channel EPIC currently recognizes as joined. Typical examples are server-mask or hostmask messages sent by an IRC operator. The classification does not verify operator status or require a particular mask syntax, so other unmatched destinations can reach this event too.

The hook receives ordinary text remaining after CTCP processing and runs only if [on general_privmsg](general_privmsg.md) permits further handling. CTCP-only messages can be consumed before either ordinary message event. Joined-channel messages use the public-message hooks instead.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender, destination, and text together. There are no event-specific options. Delivery and the meaning of group masks depend on the server.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender's nickname or prefix name. |
| `$1` | Original destination supplied in the message. |
| `$2-` | Message text remaining after CTCP processing. |

EPIC recognizes one status-prefix character advertised by the server's `statusmsg` capability when it precedes a joined channel. Such messages normally follow channel classification, while the hook arguments for any group-classified message retain its original destination.

## Default behavior

If the hook permits default handling, EPIC displays `<-sender:destination-> message`. A suppressing hook, such as a serial-zero `^msg_group` hook, prevents that line and can supply replacement output. It does not retract the received message or undo earlier CTCP processing.

The output context uses the destination at the `wall` message level. This path does not update the last private sender or last public sender: those updates apply only to personal or joined-channel classifications. It also does not use the away timestamp formatting of the personal `msg` path.

## Automated replies

The hook does not automatically reply. As with [on msg](msg.md), use [notice](../commands/notice.md) for automated responses when needed to avoid reply loops. The current built-in [msg](../commands/msg.md) sender does not automatically convert a message into a notice because this hook is running. Any script-generated request remains subject to the current connection and server policy.

## Example

Replace group-message display while retaining the destination:

```text
on ^msg_group "*" (sender, group, message) {
    echo Group message from $sender to $group: $message;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_privmsg` classifies destinations, selects the `wall` context, invokes `general_privmsg` before this event, and formats default output; `is_target_channel_wall` checks status prefixes.
- [source/commands.c](../../source/commands.c): `send_text` selects outgoing message type from the requested command without automatic conversion for this hook.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
