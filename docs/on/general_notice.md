# on general_notice

## Synopsis

```text
/on [<noise>]general_notice "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for an incoming [notice](../commands/notice.md) with its sender, destination, and text available together. It provides a common interception point before the more specific notice-display hooks, so scripts need not catch each of those separately.

Use the standard [on](../commands/on.md) matching and noise controls. The pattern matches all three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Message sender, normally a nickname or server name. |
| `$1` | Destination supplied by the server. |
| `$2-` | Notice text remaining after CTCP processing. |

For prefixless server notices, EPIC substitutes its recorded server name, falling back to the configured hostname when necessary. For notices to a status-prefixed channel such as `@#channel`, `$1` retains the original prefix even when EPIC strips it internally to classify the notice. Server-provided text and destination conventions can vary.

## Event chain and default behavior

CTCP replies are processed first; see [on ctcp_reply](ctcp_reply.md). A notice containing only a consumed CTCP payload does not reach this hook. The former encrypted-notice stage is absent in EPIC6.

For notices without a sender prefix or whose sender exactly matches the connected server's recorded name, EPIC takes the server-notice path. Certain operator messages first generate [on oper_notice](oper_notice.md), and recognized kill reports can first generate [on kill](kill.md). Suppression there prevents the later chain. Otherwise the order is:

1. `general_notice`.
2. [server_notice](server_notice.md).
3. Built-in display if both events permit it.

Other senders, including remote servers, take the ordinary path:

1. `general_notice`.
2. [public_notice](public_notice.md) when the destination identifies a joined channel, or [notice](notice.md) otherwise.
3. Built-in display if both events permit it.

Classification follows these source checks, so a notice from the connected server's recorded name takes the server-notice path even if its destination is a channel.

A suppressing hook, such as a serial-zero `^general_notice` hook, prevents the later specific hook and its built-in display. There is no direct built-in line attached to `general_notice` itself. Suppression does not retract the received message or undo CTCP handling already performed.

Ordinary notice output uses the `notice` level; the server-notice path uses `snote` for this event. Handling the event does not automatically send anything. The built-in `ctcp` command rejects invocation while processing notices.

## Example

Replace ordinary and server notice formatting at this common point:

```text
on ^general_notice * (sender, target, message) {
    echo Notice from $sender to $target: $message;
};
```

## History

This hook first appeared in EPIC4-1.1.3.

## Source references

- [source/parse.c](../../source/parse.c): `p_notice` performs CTCP processing, target normalization, and ordinary notice dispatch; `p_snotice` implements the operator and server-notice chains.
- [source/ctcp.c](../../source/ctcp.c): `do_ctcp` processes reply payloads before ordinary notice text.
- [source/commands.c](../../source/commands.c): `ctcpcmd` rejects invocation from a notice context.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
