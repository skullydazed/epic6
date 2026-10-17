# on server_notice

## Synopsis

```text
/on [<noise>]server_notice "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for a notice on the connected-server notice path when earlier hooks permit continued handling. Prefixless notices and notices whose sender exactly matches the connected server's recorded name enter this path. A remote server's notice normally takes ordinary [general_notice](general_notice.md) and client/channel notice handling instead.

The source check takes precedence over channel classification, so even a channel-addressed notice from the connected server's recorded name can reach this event. Notice delivery and text formats depend on the server.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the reporting server and the header-adjusted message together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Reporting server name. |
| `$1` | First word of the supplied message, often `***`. |
| `$2-` | Remaining supplied message text. |

For prefixless notices, EPIC substitutes its recorded server name, falling back to the configured hostname when necessary. The destination is not included; [on general_notice](general_notice.md) supplies it earlier.

If the original message begins with `*` or `#`, its text is supplied unchanged after the server name. Otherwise EPIC inserts `*** ` before it for this event. Thus `$1-` is not always identical to the original server text, and `$2-` alone can omit its original first word when it already had a header.

## Event chain

Recognized `Received KILL message for ...` operator reports first generate [on kill](kill.md). Operator notices matching the `*** Notice --` convention then generate [on oper_notice](oper_notice.md). If those stages permit continuation, processing reaches `general_notice` and then `server_notice`. Suppression at any earlier applicable stage prevents this event.

CTCP reply handling precedes notice classification. A notice consisting only of consumed CTCP payloads does not reach this chain.

## Default behavior

If this hook permits default handling, text beginning with `*` or `#` is displayed as supplied. Other original text is printed with EPIC's usual message prefix. Output uses the `snote` level with the notice destination as its context, allowing normal server-notice window routing rather than guaranteeing a particular named window.

A suppressing hook, such as a serial-zero `^server_notice` hook, prevents this built-in display and can supply replacement output. It does not retract the notice or undo earlier CTCP processing. The hook does not automatically send an IRC response. The built-in `ctcp` command specifically rejects invocation during notice processing.

## Example

Replace display while preserving the event's supplied message header:

```text
on ^server_notice "*" {
    echo Server $0: $1-;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_notice` selects the server path; `p_snotice` runs the preceding hook chain, adjusts the supplied header, invokes this event, and formats output.
- [source/commands.c](../../source/commands.c): `ctcpcmd` rejects invocation from notices.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
