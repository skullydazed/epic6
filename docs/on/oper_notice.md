# on oper_notice

## Synopsis

```text
/on [<noise>]oper_notice "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for a server notice recognized by the operator-notice parser. The usual text begins `*** Notice -- `. Whether you receive these notices, their wording, and their scope depend on the server, your privileges, and server notice settings. This hook does not independently establish that a notice is global or visible to all operators.

It runs on the connected-server notice path: prefixless notices and notices whose sender exactly matches the connected server's recorded name enter that path. Other notice senders do not generate this event merely because their text resembles an operator notice.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the server name and stripped message body together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Reporting server name. |
| `$1-` | Operator-notice body after the expected prefix. |

For a prefixless notice, EPIC uses its recorded server name, falling back to the configured hostname when necessary. The parser checks the first 13 characters against `*** Notice --` and supplies text starting at character offset 14, skipping the usual following space. It does not validate an arbitrary operator-notice format, so unusual server wording may be missed or stripped differently.

## Ordering and default behavior

Recognized `Received KILL message for ...` reports are offered to [on kill](kill.md) first. If that hook suppresses the report, `oper_notice` does not run. Otherwise the chain continues through this event.

There is no direct built-in display attached to `oper_notice`. If it permits default handling, EPIC proceeds to [on general_notice](general_notice.md), then [on server_notice](server_notice.md), and finally the ordinary server-notice display if those stages allow it. Those later stages receive the original notice text rather than this event's stripped body.

A suppressing hook, such as a serial-zero `^oper_notice` hook, prevents the later chain and its default display for that notice. It does not retract the server notice, undo earlier kill-report handling, or alter server privileges. This event uses the `opnote` output level and the notice destination as its context.

The hook does not automatically send an IRC response. The built-in `ctcp` command refuses invocation during notice processing; scripts should not generate automatic message replies to notices.

## Example

Alert the user and replace recognized operator-notice display:

```text
on ^oper_notice "*" {
    beep;
    echo --operator notice-- $1-;
};
```

The bell remains subject to the beep setting and terminal support.

## Source references

- [source/parse.c](../../source/parse.c): `p_notice` selects the connected-server path; `p_snotice` recognizes the prefix, offers kill reports first, invokes this event, and controls the later notice chain.
- [source/commands.c](../../source/commands.c): `ctcpcmd` rejects invocation from a notice context.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
