# on invite

## Synopsis

```text
/on [<noise>]invite "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC receives an IRC invitation to a channel. The event reports the incoming server message; it does not itself join the channel or accept the invitation.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the inviter, channel, and userhost together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Nickname or sender prefix of the inviter. |
| `$1` | Channel named in the invitation. |
| `$2` | Inviter's `user@host` from the message prefix, when present. |

The userhost field can be empty when the server supplies no such prefix information. The invitee field in the incoming protocol message is required but is not included in the hook arguments. The handler does not verify that it equals your nickname before invoking the hook. Missing invitee or channel parameters are rejected as malformed messages.

## Default behavior and effects

If the hook permits default handling, EPIC displays `inviter (user@host) invites you to channel channel` with its usual message prefix. A suppressing hook, such as a serial-zero `^invite` hook, prevents that line and can supply replacement output.

Before invoking the hook, EPIC records the channel as the receiving server's most recent invitation and the inviter as its most recent received nickname. Suppressing display does not undo those updates. The hook runs with the invited channel as its output context at the `invite` message level.

No automatic reply or join is sent. If a script issues a join in response, success remains subject to the server's channel rules and the current connection; receiving an invitation is not proof that a later join will succeed.

## Example

Customize the invitation's appearance:

```text
on ^invite "*" {
    xecho -b $0 cordially invites you to $1;
};
```

This changes only the local display.

## Source references

- [source/parse.c](../../source/parse.c): `p_invite` validates parameters, stores invitation and sender state, invokes the hook, and displays the default message; `BreakArgs` extracts prefix userhost information.
- [source/server.c](../../source/server.c): stores per-server invitation and received-nickname state.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
