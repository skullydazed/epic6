# on send_ctcp

## Synopsis

```text
/on [<noise>]send_ctcp "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by EPIC's CTCP sender for an outgoing request or reply, before framing and the ordinary send call. It includes CTCP actions sent by [me](../commands/me.md) or [describe](../commands/describe.md), as well as payloads sent through [ctcp](../commands/ctcp.md).

The event reports a send attempt, not confirmed delivery. A hand-built CTCP payload sent through some other command need not pass through this helper or generate this event. Use standard [on](../commands/on.md) matching and noise controls; the pattern matches protocol, target, type, and optional argument text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | `PRIVMSG` for a request or `NOTICE` for a reply. |
| `$1` | Destination supplied to the CTCP sender, normally a nickname or channel. |
| `$2` | CTCP type. |
| `$3-` | Optional CTCP argument text after outgoing character-set conversion. |

The framing delimiter characters are not included. The CTCP type is expected to be ASCII; the sender converts its argument text but not that type. Because conversion occurs before this hook, the arguments are not necessarily the original UTF-8 command text.

The hook sees argument text before final CTCP-buffer truncation. Its text can therefore be longer than the payload that is ultimately sent. No-argument sends contain only the first three fields.

## Default behavior and effects

There is no built-in display associated with this hook. Its default-handling return value is ignored, so a suppressing hook does not cancel the send. Changing handler variables does not replace the payload constructed afterward. To control the final outgoing server line, consult the separate [on send_to_server](send_to_server.md) interface and its own contract.

The hook runs at the `ctcp` output level with the destination as its output context. The sender can return before invoking it when the destination leaves no room for a CTCP payload or an earlier reply-throttling check drops the attempt. Connection failures and server restrictions can still prevent delivery after it runs.

The event is marked nonrecursive. A CTCP command executed in a handler can still attempt another send, but nested `send_ctcp` handlers do not run again. This flag is not a general limit on script-generated traffic.

## Example

Display attempted requests:

```text
on -send_ctcp "PRIVMSG *" {
    xecho -b Requesting a CTCP $2 from $1: $3-;
};
```

This adds a local message without sending a duplicate request. For action-specific local echo control, use [on send_action](send_action.md); that hook runs after the action command's CTCP send call.

## Source references

- [source/ctcp.c](../../source/ctcp.c): `send_ctcp` chooses the protocol, performs early checks and argument recoding, invokes this hook without checking its return value, frames and truncates the payload, and calls `send_text`.
- [source/commands.c](../../source/commands.c): `ctcpcmd`, `mecmd`, and `describe` use the CTCP sender; `send_text` sends the framed payload.
- [source/hook.c](../../source/hook.c): the event registration sets the nonrecursion flag; dispatch implements matching.
