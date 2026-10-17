# on kill

## Synopsis

```text
/on [<noise>]kill "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC recognizes an operator server notice reporting that someone was killed from IRC. This depends on receiving a notice in the textual format understood by the parser; it is not a notification for every kill on the network. Whether such notices are sent to you depends on the server, privileges, and notice settings.

The handler looks for the operator-notice prefix `*** Notice -- ` and the report text `Received KILL message for ` on the connected-server notice path. Different server wording can bypass this event. It is distinct from receiving an actual IRC `kill` command directed at you, which uses [on disconnect](disconnect.md) for its operator-style notification.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches all five fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Reporting server name, normally your connected server. |
| `$1` | Victim's nickname. |
| `$2` | Operator or source named after `From` in the report. |
| `$3` | Reported path to that source, or `*` when no `Path:` field is recognized. |
| `$4-` | Remaining reason text. |

A trailing period on the victim token is removed. The parser accepts `From` and `Path:` case-insensitively. It takes the path as one word and leaves the remainder as reason text, without interpreting or removing reason punctuation. These fields are parsed from a server's human-readable report, not independently verified protocol attributes.

## Default behavior and suppression

This event has no direct built-in line. If it permits default handling, processing continues through [on oper_notice](oper_notice.md), [on general_notice](general_notice.md), and [on server_notice](server_notice.md), as long as each permits the next stage. The original notice is displayed if that chain allows it.

A suppressing hook, such as a serial-zero `^kill` hook, stops that notice chain and its default display. It does not reverse the kill, disconnect anyone, or send an IRC request. The hook runs at the `opnote` message level with the server notice's destination as its output context.

If the recognized report lacks the expected `From` portion, EPIC prints an ill-formed-report diagnostic and falls back to the remaining operator-notice chain without generating `kill`.

## Example

Display recognized kill reports in a simpler form:

```text
on ^kill "*" {
    xecho -b $1 was killed by $2: ($4-);
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_snotice` recognizes the report prefix and controls the notice chain; `p_killmsg` extracts fields and invokes this event; `p_kill` separately handles actual IRC kill commands.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
