# on public_notice

## Synopsis

```text
/on [<noise>]public_notice "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for ordinary text in an incoming [notice](../commands/notice.md) addressed to a channel EPIC recognizes as joined, when the message takes the ordinary notice path. The sender may be on or off that channel; there is no sender-membership check for this event.

Prefixless notices and notices whose sender exactly matches the connected server's recorded name take the separate server-notice path, even when addressed to a channel. Other senders, including remote servers, can generate `public_notice` for joined-channel destinations. Non-channel destinations use [on notice](notice.md).

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches sender, original destination, and text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Message sender, normally a nickname. |
| `$1` | Original channel destination from the server message. |
| `$2-` | Notice text remaining after CTCP processing. |

A status prefix advertised by the server, such as `@` in `@#channel`, can be stripped internally to recognize the joined channel. `$1` retains the original prefixed destination.

## Ordering and default behavior

CTCP replies are processed first. A notice containing only consumed CTCP payloads does not reach ordinary notice hooks; see [on ctcp_reply](ctcp_reply.md).

[On general_notice](general_notice.md) runs before this event. Its suppression skips `public_notice`; otherwise both events can run for the same notice. They are not inherently mutually exclusive.

If this hook permits default handling, EPIC displays `-sender:destination- message`, including the destination regardless of whether the channel is current in its window. A suppressing hook, such as a serial-zero `^public_notice` hook, prevents that line and can provide replacement output. It does not retract the notice or undo earlier CTCP handling.

Output uses the normalized joined channel's context at the `notice` message level. Handling this event does not automatically send a response or change channel membership.

## Example

Distinguish notices to the current channel from other channel destinations:

```text
on ^public_notice "*" (sender, channel, message) {
    if (channel == C) {
        echo +$sender+ $message;
    } else {
        echo +$sender:$channel+ $message;
    };
};
```

The comparison uses the original destination, so a status-prefixed channel can take the second branch.

## Automated responses

Do not automatically answer notices with messages, which can cause reply loops. The built-in `ctcp` command specifically rejects invocation while processing a notice, printing `You may not use the CTCP command from a NOTICE!`. The current ordinary `msg` and `notice` sending path does not enforce the same blanket rejection; scripts should not depend on every outgoing command being blocked here.

## Source references

- [source/parse.c](../../source/parse.c): `p_notice` processes CTCP replies, checks connected-server notices, classifies joined-channel destinations without a sender-membership test, and invokes the general and specific hooks.
- [source/commands.c](../../source/commands.c): `ctcpcmd` rejects invocation from notices; `send_text` handles ordinary outgoing messages without a blanket notice-context rejection.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
