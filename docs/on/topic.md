# on topic

## Synopsis

```text
/on [<noise>]topic "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the client receives a server `topic` message reporting a changed channel topic. Normally this concerns a channel you are on, but the handler does not require the channel to exist in EPIC's membership records. Your own changes also use this hook when reported back by the server.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender, channel, and new topic together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender of the topic change, normally the nickname of the person changing it. |
| `$1` | Channel whose topic changed. |
| `$2-` | New topic text; empty when the topic is cleared. |

The hook arguments do not include the sender's userhost, the old topic, or a timestamp. A message missing its channel or topic parameter is rejected before this hook runs; an explicitly empty topic is accepted.

## Default behavior

If the hook permits default handling, EPIC displays `sender has changed the topic on channel channel to topic` with its usual message prefix. A suppressing hook, such as a serial-zero `^topic` hook, prevents that line. Suppression does not reverse the server's topic change. The hook runs in the receiving server's context with the channel as its output target and the `topic` message level.

The hook sends no request. Issuing [topic](../commands/topic.md) forwards a topic query or change request to the server; permissions, accepted text, and resulting replies depend on the server. Sending the command does not itself trigger this event.

Numeric replies describing an existing topic, such as `332` and the setter/time reply `333`, follow numbered hooks and [on numeric](numeric.md), rather than this hook. In particular, receiving the existing topic when joining a channel does not by itself constitute an `on topic` event.

## Example

Replace topic-change output:

```text
on ^topic "*" (nick, chan, text) {
    echo Topic on $chan changed by $nick: $text;
};
```

An empty `$text` in this example indicates that the topic was cleared.

## Source references

- [source/parse.c](../../source/parse.c): `p_topic` validates parameters, sets output context, invokes the hook, and displays the default line.
- [source/commands.c](../../source/commands.c): `e_topic` sends topic queries and changes.
- [source/numbers.c](../../source/numbers.c): numeric topic replies use separate numeric handling.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching and suppression.
