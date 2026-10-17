# on nickname

## Synopsis

```text
/on [<noise>]nickname "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered once for an observed nickname change, rather than once per shared channel, provided no preceding [on channel_nick](channel_nick.md) event suppressed combined handling. It includes your own accepted nickname changes as well as other people's changes reported by the server.

This event processes incoming IRC `nick` messages. Sending [nick](../commands/nick.md) does not itself trigger it, and initial nickname acceptance during registration does not use this event. See [on new_nickname](new_nickname.md) for choosing a replacement after a pre-registration rejection.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the old and new nickname together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Old nickname. |
| `$1` | New nickname. |

No channel, userhost, or server refnum is included in these arguments. The hook runs in the receiving server's context.

## Ordering and default behavior

For your own change, EPIC records the new accepted server nickname before the channel hooks and this event. Thus [servernick](../functions/servernick.md) already returns your new nickname; compare `$1`, rather than `$0`, when distinguishing your own change.

EPIC first invokes `channel_nick` for each shared channel in its cache. If any of those events suppresses default handling, `nickname` and its built-in display are skipped for the entire change. Otherwise this event runs once, even when no shared channel was found.

If the hook permits default handling, EPIC displays `oldnick is now known as newnick` with its usual message prefix. A suppressing hook, such as a serial-zero `^nickname` hook, prevents that line. Suppression does not reject the server-side change or stop cache updates.

Channel nickname lists are renamed after these events finish, so queries during this hook can still find the old nickname in channel membership. Your own accepted nickname and the channel caches can therefore temporarily reflect different stages of the change.

The output level is `nick`. Your own change has no channel target in its output context; another person's change uses a shared channel found from the cache when one exists. Handling the event does not automatically send an IRC request.

## Example

Distinguish your own accepted change from another user's:

```text
on ^nickname "*" (oldnick, newnick) {
    if (newnick == servernick()) {
        xecho -b Your nickname is now $newnick;
    } else {
        xecho -b $oldnick has changed nicks to $newnick;
    };
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_nick` accepts your new nickname, dispatches per-channel events, conditionally invokes `nickname`, and updates channel lists afterward.
- [source/names.c](../../source/names.c): `walk_channels`, `what_channel`, and `rename_nick` provide shared-channel lookup and cache updates.
- [source/server.c](../../source/server.c): `accept_server_nickname` records accepted nickname state.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
