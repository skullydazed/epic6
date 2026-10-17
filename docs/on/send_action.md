# on send_action

## Synopsis

```text
/on [<noise>]send_action "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by [me](../commands/me.md) and [describe](../commands/describe.md) after they call the CTCP sender for an outgoing action. It controls their action-specific local echo. Sending a CTCP action directly with `/ctcp <target> action ...` does not generate this event.

The event indicates a command's attempted send, not confirmed delivery. Connection errors, payload limits, and server policy can prevent or alter delivery even though this hook runs. The hook is invoked after the send call, so suppressing it cannot cancel the outgoing action.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches target and action text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Action target, normally a nickname or channel. |
| `$1-` | Original action-description text supplied by the command. |

`me` selects the current window's target, preferring its query target when present. `describe` uses its explicit target; `*` resolves to the current channel rather than the query. Missing text or an unavailable `me` target is rejected before this hook. The command text passed here can be longer than the payload actually sent, because CTCP framing applies its own size limit.

## Default behavior and suppression

If the hook permits default handling, `me` displays `* yournick description`. `describe` instead displays `* -> target: yournick description`. Both use the `action` message level with their selected target as the output context.

A suppressing hook, such as a serial-zero `^send_action` hook, prevents that local echo and can provide replacement output. It does not retract the send or suppress earlier [on send_ctcp](send_ctcp.md) processing in the CTCP sender.

The hook is marked nonrecursive. An action command executed within a handler can still attempt another send, but its nested `send_action` handlers do not run again. This is not a general guard against scripts repeatedly generating actions.

## Example

Display separate local messages for channel and private actions:

```text
on ^send_action "*" {
    if (ischannel($0)) {
        echo * $0: $servernick() $1-;
    } else {
        echo *> $0: $servernick() $1-;
    };
};
```

This replaces the commands' local echo without sending the action again.

## Source references

- [source/commands.c](../../source/commands.c): `mecmd` and `describe` select targets, call `send_ctcp`, invoke this hook, and provide their different local echoes.
- [source/ctcp.c](../../source/ctcp.c): `send_ctcp` frames and limits outgoing payloads and invokes the generic CTCP send hook.
- [source/hook.c](../../source/hook.c): the event registration sets the nonrecursion flag; dispatch implements matching and suppression.
