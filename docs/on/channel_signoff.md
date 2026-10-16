# on channel_signoff

## Synopsis

```text
/on [<noise>]channel_signoff "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when someone on one of your channels [quits](../commands/quit.md) IRC or is disconnected, as reported by an incoming server `quit` message. If the person shares several channels with you, EPIC generates one event per channel in its membership records on the receiving server.

This is not the event for your own client exit; use [on exit](exit.md). Losing your server connection does not generate a signoff event for every person on that server. See [on channel_lost](channel_lost.md) for loss of your own channel state.

Use the standard [on](../commands/on.md) matching and noise controls. The pattern matches the channel, nickname, and message together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Channel where the signoff was observed. |
| `$1` | Nickname of the person leaving IRC. |
| `$2-` | Signoff message supplied by the server. |

The message can be empty. A malformed `quit` message with no message parameter is rejected before this hook runs. The reason's text and meaning depend on the server; it need not be a message chosen by the departing person.

## Default behavior

This event has no direct built-in output. After all channel events for a quit have run, EPIC generates one [on signoff](signoff.md) event only if **none** of them suppressed default handling. If `signoff` also allows default handling, EPIC prints `Signoff: nickname (message)` with its usual message prefix.

Suppressing a single channel event prevents that later `signoff` event and its built-in line for the entire quit. It does not stop the remaining channel events or prevent EPIC from removing the nickname from its channel lists. If the nickname appears in no channel records, no channel event runs, but the `signoff` event can still run.

The channel hook runs with the shared channel as its output context and the `quit` message level. Nickname records are removed after the channel hooks and any subsequent `signoff` hook finish, so membership queries during a handler may still find the departing nickname. This hook does not send an IRC request.

## Example

Distinguish signoffs by channel while replacing the built-in line:

```text
on ^channel_signoff * (chan, nick, message) {
    if (iscurchan($chan)) {
        xecho -b Signoff by $nick ($message);
    } else {
        xecho -b Signoff by $nick from $chan ($message);
    };
};
```

A person sharing multiple channels can produce multiple replacement lines. Use `on signoff` for one notification per quit, and ensure channel hooks permit it to run.

## Source references

- [source/parse.c](../../source/parse.c): `p_quit` invokes per-channel hooks, conditionally invokes `signoff`, and then removes the nickname.
- [source/names.c](../../source/names.c): `walk_channels` finds shared channels; `remove_from_channel` removes the nickname from all channels on the server.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard hook matching and suppression.
- [source/irc.c](../../source/irc.c): client exit invokes the separate `exit` hook.
