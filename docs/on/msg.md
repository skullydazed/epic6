# on msg

## Synopsis

```text
/on [<noise>]msg "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for ordinary text in an incoming [private message](../commands/msg.md) addressed to your nickname, after CTCP processing and [on general_privmsg](general_privmsg.md). Suppression by `general_privmsg` prevents this event. A message consisting only of consumed CTCP payloads does not reach the ordinary message hook.

Channel messages use the public-message events, and other destinations use [on msg_group](msg_group.md). For a hook that includes the original destination as an argument, use `general_privmsg`.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender and message text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1-` | Message text remaining after CTCP processing. |

The destination is not included in these arguments. Before either message hook runs, EPIC records the sender as the receiving server's most recent private sender. Suppressing display does not undo that update.

## Default behavior

If the hook permits default handling, EPIC displays `*sender* message`. If any registered server has a stored away message, it appends a local receipt timestamp in angle brackets. This away-format decision checks all registered servers, not only the server carrying this message.

A suppressing hook, such as a serial-zero `^msg` hook, prevents this built-in line and can supply replacement output. It does not retract the message, change its sender, or automatically reply. The output context uses the sender as the target at the `msg` message level, allowing normal private-message window routing.

## Automated replies

Use [notice](../commands/notice.md) rather than an ordinary `msg` for automated responses when needed to avoid reply loops. The current built-in sender does not automatically convert `msg` into `notice` because this hook is running. Do not rely on that historical behavior. A script's outgoing request remains subject to server delivery rules and connection state.

The generic `ctcp` command does choose notice reply framing while processing an incoming private message; that is separate command behavior, not conversion of all outgoing messages.

## Example

Replace the private-message display without sending a response:

```text
on ^msg "*" (sender, message) {
    echo Private message from $sender: $message;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_privmsg` classifies personal messages, records the last sender, invokes `general_privmsg` and then `msg`, and formats default output.
- [source/server.c](../../source/server.c): `get_server_away_message(NOSERV)` checks stored away messages across registered servers.
- [source/commands.c](../../source/commands.c): `send_text` chooses outgoing message type from the requested command; `ctcpcmd` selects CTCP reply framing.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
