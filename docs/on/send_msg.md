# on send_msg

## Synopsis

```text
/on [<noise>]send_msg "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by the ordinary outgoing message sender for a non-channel `privmsg` target when that call enables send hooks. This normally covers [msg](../commands/msg.md) to another nickname. Channel targets use [on send_public](send_public.md), and outgoing notices use [on send_notice](send_notice.md).

Classification checks whether the target is a channel; it does not verify that a non-channel destination is an individual user. Group masks or other non-channel destinations handled by this path can therefore generate this event too.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches destination and original text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Destination currently being processed, normally a nickname. |
| `$1-` | Original message text before outgoing character-set conversion. |

For a comma-separated target list, the sender processes each destination separately. The hook sees the full original text once for that destination before its send loop can split encoded text into multiple payload chunks. Empty text, the special sink target `0`, and non-IRC destinations can bypass the event.

## Default behavior and effects

If the hook permits default handling, EPIC displays `*target*> message` locally. A suppressing hook, such as a serial-zero `^send_msg` hook, prevents that local echo, **not the send**. The outgoing send loop runs afterward regardless of the hook's default-handling return value. Handler variable changes do not replace the already prepared encoded text.

The event is an attempted-send notification, not confirmation of delivery. The connection, later send processing, and server policy can still prevent delivery. Consult [on send_to_server](send_to_server.md) for the separate final server-line hook and its contract.

Output uses the destination's context at the `msg` message level. After attempting the send loop, EPIC records the destination as the server's most recent sent nickname, even when local echo was suppressed. The hook itself does not generate an additional reply.

## Limits and recursion

Internal callers can disable send hooks and local echo, including the CTCP sending path. This event is therefore not a complete record of every outgoing private-message wire line.

The hook is marked nonrecursive, and the ordinary sender also disables send hooks for a nested send call and refuses deeper ordinary recursion. A handler that sends a message can still attempt that nested send; these guards do not turn suppression into a delivery veto.

## Example

Replace the local outgoing private-message echo:

```text
on ^send_msg "*" (target, message) {
    echo To $target: $message;
};
```

Do not send the same text again in this handler unless a duplicate outgoing message is intended.

## Source references

- [source/commands.c](../../source/commands.c): `send_text` splits targets, classifies message type, invokes the hook before sending chunks, records the last sent target, and enforces recursion handling.
- [source/ctcp.c](../../source/ctcp.c): `send_ctcp` calls `send_text` with ordinary send hooks disabled.
- [source/hook.c](../../source/hook.c): the event registration sets the nonrecursion flag; dispatch implements matching and default-echo suppression.
