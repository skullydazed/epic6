# on public

## Synopsis

```text
/on [<noise>]public "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for ordinary incoming channel-message text when the channel is current in its owning window and the message takes EPIC's regular public-message path. This can include channels current in other windows; it is not limited to the globally selected input window.

A sender absent from the cached membership list takes [on public_msg](public_msg.md) instead when the channel's cached mode `+n` is absent. With `+n` present, that outsider check is skipped. Joined channels that are not current in their owning window use [on public_other](public_other.md). These classifications use the receiving server's cached channel state.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender, original destination, and text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1` | Original channel destination from the message. |
| `$2-` | Message text remaining after CTCP processing. |

A server-advertised status prefix such as `@` can be stripped internally to classify a message to `@#channel`, but `$1` retains the original prefixed destination. The text does not include CTCP payloads already consumed by the parser.

## Ordering and default behavior

CTCP processing runs first, and CTCP-only messages can bypass ordinary message hooks entirely. [On general_privmsg](general_privmsg.md) then runs before this event; its suppression prevents `public` from running. The receiving server's most recent public sender is updated before both hooks, even if display is later suppressed.

If this hook permits default handling, EPIC displays `<sender> message` without the channel name. This remains the selected format for a status-prefixed destination classified as current-channel public output. A suppressing hook, such as a serial-zero `^public` hook, prevents that line and can provide replacement output.

The output context uses the normalized channel at the `public` message level. Suppression does not retract the message, undo earlier CTCP processing, or remove the sender-state update. The hook does not automatically reply; any script-generated IRC request depends on the connection and server policy.

## Example

Replace regular current-channel message display while showing the destination:

```text
on ^public "*" (sender, channel, message) {
    echo <$sender/$channel> $message;
};
```

This does not replace `public_other` or `public_msg` display. Use `general_privmsg` when you need a common interception point across those classifications.

## Source references

- [source/parse.c](../../source/parse.c): `p_privmsg` processes CTCP payloads, classifies channel messages, stores the public sender, invokes the general and specific hooks, and formats output; `is_target_channel_wall` checks status prefixes.
- [source/names.c](../../source/names.c): `is_current_channel` tests the owning window's current channel; `is_channel_nomsgs` checks cached `+n`; `is_on_channel` checks membership.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
