# on send_public

## Synopsis

```text
/on [<noise>]send_public "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by the ordinary outgoing message sender for a channel `privmsg` target when that call enables send hooks. This normally covers text sent to the current channel or [msg](../commands/msg.md) with an explicit channel target.

The classification uses channel-name syntax, not a joined-channel membership check. An attempt to send to an unjoined channel can therefore generate this event; whether the server permits delivery depends on its channel rules. Non-channel targets use [on send_msg](send_msg.md), and notices use [on send_notice](send_notice.md).

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches destination and original text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Channel destination currently being processed. |
| `$1-` | Original message text before outgoing character-set conversion. |

Comma-separated destinations are processed individually. The event sees the original text once per eligible destination before the encoded text can be divided into several outgoing payload chunks. Empty text and non-IRC special destinations can bypass the event.

Channel recognition uses the server's `chantypes` information when available, with conventional channel-prefix defaults otherwise. This sender does not strip an incoming-style status prefix before classifying its target, so a destination such as `@#channel` need not select `send_public`.

## Default behavior and effects

If the hook permits default handling, EPIC displays `channel> message`. A suppressing hook, such as a serial-zero `^send_public` hook, prevents that local echo but does not cancel the send. The outgoing send loop runs afterward regardless of the hook's default-handling return value. Handler variable changes do not replace the already prepared encoded text.

The output context uses the destination at the `public` message level. After attempting the send loop, EPIC records the destination as the server's most recent sent nickname, even when local echo was suppressed. The event is not a delivery acknowledgment: later connection failures, send handling, and server restrictions can prevent delivery. See [on send_to_server](send_to_server.md) for the separate final server-line hook.

## Limits and recursion

Internal callers can disable ordinary send hooks. The CTCP sender does so, meaning actions sent through [me](../commands/me.md) or [describe](../commands/describe.md) do not generate this ordinary channel-text event; see [on send_action](send_action.md) and [on send_ctcp](send_ctcp.md).

The hook is marked nonrecursive. The ordinary sender also disables hooks for a nested send call and refuses deeper ordinary recursion. A handler can still attempt another send, so do not send the text again merely to customize its local echo.

## Example

Replace outgoing channel-message display:

```text
on ^send_public "*" (channel, message) {
    echo To $channel: $message;
};
```

## Source references

- [source/commands.c](../../source/commands.c): `send_text` selects the channel hook, controls echo, sends encoded chunks afterward, records the target, and handles recursion.
- [source/parse.c](../../source/parse.c): `is_channel` checks `chantypes` and fallback channel prefixes without checking membership.
- [source/ctcp.c](../../source/ctcp.c): `send_ctcp` disables ordinary send hooks for framed payloads.
- [source/hook.c](../../source/hook.c): event registration sets the nonrecursion flag; dispatch implements matching and echo suppression.
