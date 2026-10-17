# on public_msg

## Synopsis

```text
/on [<noise>]public_msg "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for ordinary incoming text to a joined channel when the sender is absent from EPIC's cached membership list and the channel's cached mode `+n` is absent. This is the client's outsider-message classification, not independent proof of actual server-side membership.

The check takes precedence over current-channel selection, so it applies whether or not the channel is current in its window. If cached `+n` is present, EPIC skips this outsider check and selects [on public](public.md) or [on public_other](public_other.md) instead. The server determines whether an outside sender may send to the channel; this hook only processes a delivered message.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches sender, original destination, and text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1` | Original channel destination supplied in the message. |
| `$2-` | Ordinary message text remaining after CTCP processing. |

A server-advertised status prefix, such as `@` in `@#channel`, can be stripped internally to recognize a joined channel, but `$1` retains the original prefix.

## Ordering and default behavior

CTCP processing occurs first. A message consisting only of consumed CTCP payloads does not reach ordinary message hooks. [On general_privmsg](general_privmsg.md) then runs before this hook; suppression there prevents `public_msg` from running.

Before either ordinary hook, EPIC records the sender as the receiving server's most recent public sender. Suppressing display does not undo that update.

If the hook permits default handling, EPIC displays `(sender/destination) message`, including the destination even for a current channel. A suppressing hook, such as a serial-zero `^public_msg` hook, prevents that line and can supply replacement output. It does not retract the message, alter membership, or reverse earlier CTCP handling.

Output uses the normalized joined channel's context at the `public` message level. The hook does not automatically reply; any script-generated IRC request requires a suitable connection and remains subject to server policy.

## Example

Customize outsider-message display:

```text
on ^public_msg "*" (sender, channel, message) {
    if (channel == C) {
        xecho -b ($sender) $message;
    } else {
        xecho -b ($sender:$channel) $message;
    };
};
```

This compares the original destination with the current channel; a status-prefixed destination can therefore take the second display branch.

## Source references

- [source/parse.c](../../source/parse.c): `p_privmsg` performs CTCP handling, outsider classification, sender-state updates, hook dispatch, and default formatting; `is_target_channel_wall` recognizes server status prefixes.
- [source/names.c](../../source/names.c): `is_channel_nomsgs` tests cached `+n`, and `is_on_channel` checks cached membership.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
