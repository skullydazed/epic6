# on pong

## Synopsis

```text
/on [<noise>]pong "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for an incoming IRC protocol `pong` message that passes parameter validation and is not consumed by internal wait handling. The sender is normally a server. Whether a server routes messages of this kind from another client depends on its protocol behavior.

This event is separate from a CTCP ping reply, which follows [on ctcp_reply](ctcp_reply.md). Use standard [on](../commands/on.md) matching and noise controls. The pattern matches all three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender prefix of the pong. |
| `$1` | First pong argument, called the pong server by the handler. |
| `$2-` | Remaining pong arguments joined into text. |

Both the first and second protocol arguments must be present; a one-argument pong is reported as malformed rather than generating this hook. The values are server-provided protocol fields, not a guaranteed measured round-trip time.

When the sender matches the connected server's recorded name case-insensitively, EPIC first checks the remaining text against its outstanding hard or soft wait tokens. A matched response completes that wait and bypasses `pong`. The old comment that pong is not used for waits does not describe this active check.

## Default behavior

If the hook permits default handling and the **first pong argument** contains a dot, EPIC displays `first-argument: PONG received from sender (remaining-text)` with its usual message prefix. If that argument contains no dot, there is no built-in line even though the hook runs. This is a text heuristic, not validation that the sender is a server.

A suppressing hook, such as a serial-zero `^pong` hook, prevents that default display and can provide replacement output. It does not block network traffic, rate-limit the sender, undo a completed wait, or send an IRC response.

## Example

Display only pong senders whose names contain a dot, replacing the built-in display:

```text
on ^pong "*" {
    if (rmatch($0 %.%)) {
        xecho -b Received protocol PONG from $0: $1-;
    };
};
```

This filters local output based on sender text. It is not flood protection and does not authenticate the sender.

## Source references

- [source/parse.c](../../source/parse.c): `p_pong` validates two arguments, checks internal waits, invokes the hook, and uses the first argument's dot test for default display.
- [source/server.c](../../source/server.c): `server_check_wait` recognizes outstanding wait tokens, unlocks hard waits, or executes soft-wait callbacks.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
