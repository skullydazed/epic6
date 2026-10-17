# on signoff

## Synopsis

```text
/on [<noise>]signoff "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the client observes another client signing off from IRC, normally through an incoming server `quit` message. It runs once per quit, not once per shared channel, provided no preceding [on channel_signoff](channel_signoff.md) event suppressed combined handling.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the nickname and signoff message together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Nickname of the person signing off. |
| `$1-` | Signoff message supplied by the server. |

The message may be empty. A `quit` message without a message parameter is rejected before this hook runs. The reason depends on the server and need not have been chosen by the departing person. No channel or userhost is included in the hook arguments.

## Ordering and default behavior

EPIC first runs `channel_signoff` for each shared channel in its membership records on the receiving server. If any of those events suppresses default handling, this event and its built-in line are skipped for the entire quit. Otherwise `signoff` runs once, even if the nickname was not found in any channel records.

If this hook permits default handling, EPIC displays `Signoff: nickname (message)` with its usual message prefix. A suppressing hook, such as a serial-zero `^signoff` hook, replaces that line. Suppression does not prevent removal of the nickname from EPIC's channel membership records; that removal happens after the hooks finish, so membership queries within a handler can still find the departing nickname.

The hook runs in the receiving server's context with the `quit` message level and a shared channel as its output target when one can be found. It does not send an IRC request.

This is not the notification for your own normal client exit; use [on exit](exit.md). Losing your server connection does not generate a signoff for every person on that server; see [on channel_lost](channel_lost.md) for loss of your own channel state.

## Example

Replace the combined signoff line:

```text
on ^signoff "*" (nick, message) {
    echo $nick left IRC ($message);
};
```

Ensure any `channel_signoff` handlers permit combined handling if this event should run.

## Source references

- [source/parse.c](../../source/parse.c): `p_quit` dispatches channel events, conditionally invokes `signoff`, displays the default line, and removes membership records.
- [source/names.c](../../source/names.c): `walk_channels`, `what_channel`, and `remove_from_channel` provide membership lookup and removal.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching and suppression.
