# on notice

## Synopsis

```text
/on [<noise>]notice "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for ordinary text from an incoming [notice](../commands/notice.md) that takes the non-channel notice path. It normally reports notices from another client addressed to you. Other destinations that are not recognized as joined channels can also select this hook; it is not restricted to your nickname.

Notices to a joined channel use [on public_notice](public_notice.md). Prefixless notices and notices whose sender exactly matches the connected server's recorded name take the separate [server_notice](server_notice.md) path, including when the destination is a channel. Remote server senders can still take the ordinary notice path.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the sender and text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Message sender, normally a nickname. |
| `$1-` | Notice text remaining after CTCP processing. |

The destination is not included. Use [on general_notice](general_notice.md) when it is needed.

## Ordering and default behavior

CTCP replies are processed before ordinary notice text. A notice consisting only of consumed CTCP payloads does not reach this hook; see [on ctcp_reply](ctcp_reply.md). There is no former encrypted-notice stage in EPIC6.

EPIC invokes `general_notice` first. If that event suppresses default handling, `notice` is skipped. Otherwise both events can run for the same message; they are not inherently mutually exclusive.

If this hook permits default handling, EPIC displays `-sender- message`. A suppressing hook, such as a serial-zero `^notice` hook, prevents that line and can supply replacement output. It does not retract the notice or undo earlier CTCP processing.

The output level is `notice`. A notice addressed to your nickname uses the sender as its routing target; other non-channel destinations retain their destination as the target. The hook does not automatically reply.

## Automated responses

Do not automatically answer notices with more messages: notice-reply loops are a protocol concern. The built-in `ctcp` command specifically refuses invocation while processing a notice, printing `You may not use the CTCP command from a NOTICE!`. The current ordinary `msg` and `notice` sending path does not enforce the same blanket rejection, so do not assume every outgoing command is blocked here.

## Example

Replace ordinary non-channel notice display without replying:

```text
on ^notice "*" (sender, message) {
    echo Notice from $sender: $message;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_notice` processes CTCP replies, classifies the destination, invokes `general_notice`, and conditionally invokes this hook and its default display; `p_snotice` handles connected-server notices separately.
- [source/commands.c](../../source/commands.c): `ctcpcmd` rejects invocation from notices; `send_text` handles ordinary outgoing messages without a blanket notice-context rejection.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
