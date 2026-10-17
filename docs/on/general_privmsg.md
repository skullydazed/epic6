# on general_privmsg

## Synopsis

```text
/on [<noise>]general_privmsg "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when an incoming IRC `privmsg` has ordinary message text remaining after CTCP processing. It supplies the sender, original destination, and text together before EPIC invokes the more specific private or public message hook.

CTCP-only messages can be consumed without reaching this event; see [on ctcp_request](ctcp_request.md) and [on action](action.md). EPIC6 has no former encrypted-private-message hook stage.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches all three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Message sender's nickname. |
| `$1` | Original destination supplied in the `privmsg`. |
| `$2-` | Message text remaining after CTCP processing. |

For a status-prefixed channel destination such as `@#channel`, `$1` retains the prefix. EPIC can strip one prefix recognized by the server's `statusmsg` capability internally when classifying a message to a joined channel. The channel and sender classifications use the client's cached state on the receiving server.

## Default behavior

If this event permits default handling, EPIC invokes one specific event:

| Destination and sender | Next event |
| --- | --- |
| Joined channel without cached mode `+n`, with sender absent from its cached membership list. | [public_msg](public_msg.md) |
| Joined channel otherwise, current in its owning window. | [public](public.md) |
| Joined channel otherwise, not current in its owning window. | [public_other](public_other.md) |
| Your nickname. | [msg](msg.md) |
| Any other destination. | [msg_group](msg_group.md) |

The outsider check is skipped when cached mode `+n` is present, so an absent sender does not always select `public_msg`. If the specific event also permits default handling, EPIC formats and displays the message. There is no direct built-in display attached to `general_privmsg` itself.

A suppressing hook, such as a serial-zero `^general_privmsg` hook, prevents the specific event and its built-in line, including `public_msg`. It does not undo earlier CTCP processing. EPIC updates the last public sender or last private sender, as applicable, before invoking this hook, even when display is suppressed.

The output context uses the normalized channel at the `public` level, the sender at the `msg` level for personal messages, or the destination at the `wall` level for other destinations. The hook does not automatically send a response.

## Sending replies

Use [notice](../commands/notice.md) for automated responses when needed to avoid message-reply loops. The current built-in [msg](../commands/msg.md) sending path does not automatically convert a message into a notice merely because this hook is running. The generic `ctcp` command does choose notice reply framing during incoming private-message processing; that is separate command behavior.

## Example

Replace the normal display while preserving the original destination in your output:

```text
on ^general_privmsg * (sender, target, message) {
    echo Message from $sender to $target: $message;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_privmsg` processes CTCP payloads, classifies the message, updates sender state, invokes this hook, and conditionally dispatches the specific event; `is_target_channel_wall` checks `statusmsg`.
- [source/names.c](../../source/names.c): `is_channel_nomsgs`, `is_on_channel`, and `is_current_channel` provide cached classification checks.
- [source/commands.c](../../source/commands.c): `send_text` selects outgoing message type from the requested command; `ctcpcmd` handles CTCP reply framing.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
