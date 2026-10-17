# on join

## Synopsis

```text
/on [<noise>]join "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the server reports your own channel join or another client's join to a channel you can observe. Sending [join](../commands/join.md) does not itself generate this event before the server reports success.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the nickname, channel, userhost, and optional compatibility status text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Nickname of the person joining. |
| `$1` | Channel joined. |
| `$2` | Joining person's `user@host` from the message prefix, when present. |
| `$3-` | Optional historical channel-status text, normally empty. |

The userhost can be empty if absent from the prefix; it is not fetched by this hook. The historical Avalon 2.9 compatibility path recognizes status markers in a bell-suffixed channel argument and supplies `(+o)`, `(+v)`, or both as trailing status text. This is not a general report of initial channel privileges on modern servers. Additional extended-join account and realname parameters are not exposed by this handler.

Missing channel parameters and a channel argument exactly equal to `0` are rejected as malformed messages rather than generating this event.

## Default behavior and timing

If the hook permits default handling, EPIC displays `nickname (user@host) has joined channel channel`, followed by compatibility status text when present, with its usual message prefix. A suppressing hook, such as a serial-zero `^join` hook, prevents the default line.

State updates happen before the hook. For another person's join, EPIC adds their nickname and prefix userhost to the channel cache. For your own join, EPIC creates or resets the channel record, assigns it to a window through the channel-claim machinery, marks it as syncing, and requests channel modes. The full membership and userhost caches are not necessarily ready at your join event; use [on channel_sync](channel_sync.md) for initial synchronization completion and its limitations.

EPIC also records the most recent joining nickname before invoking the hook. Suppression does not undo these updates, cancel the mode request, or reverse the server-side join. The hook's output context is the channel at the `join` message level.

## Example

Distinguish your own join from another user's join:

```text
on ^join "*" (nick, channel, userhost, status) {
    if (nick == servernick()) {
        xecho -b You have joined channel $channel;
    } else {
        xecho -b $nick ($userhost) has joined channel $channel;
    };
};
```

This changes local display without sending an additional IRC request.

## Source references

- [source/parse.c](../../source/parse.c): `p_join` validates the channel, updates state, handles historical status markers, requests modes for your join, and invokes the hook; `BreakArgs` extracts prefix userhost information.
- [source/names.c](../../source/names.c): `add_channel`, `add_to_channel`, and `add_userhost_to_channel` update channel state and start synchronization.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
