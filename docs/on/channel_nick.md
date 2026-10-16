# on channel_nick

## Synopsis

```text
/on [<noise>]channel_nick "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered each time someone, including you, changes nickname while recorded on one of your channels. If you share several channels with that person, EPIC generates one event for each matching channel on the receiving server. The event depends on the client's channel membership records; a nickname change with no matching channel produces no `channel_nick` event.

This event processes an incoming server `nick` message. Merely issuing [nick](../commands/nick.md) does not trigger it before the server accepts the change. Use the standard [on](../commands/on.md) pattern and noise controls; the pattern matches all three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Channel shared with the person changing nickname. |
| `$1` | Old nickname. |
| `$2` | New nickname. |

EPIC updates its own server nickname before invoking these hooks for your change, so [servernick](../functions/servernick.md) already returns your new nickname. Channel nickname lists are renamed after the channel hooks and any subsequent `nickname` hook run.

## Default behavior

This event has no direct built-in display. After running all channel events for a nickname change, EPIC generates one [on nickname](nickname.md) event only if **none** of the channel events suppressed default handling. If that event also permits default handling, EPIC prints `oldnick is now known as newnick` with its usual message prefix.

Suppressing a single channel event prevents the later `nickname` event and its built-in line for the whole change, but does not prevent the remaining channel events or the internal nickname update. If no channel event was generated, the `nickname` event can still run. The channel event's output context uses the shared channel and the `nick` message level; replacement output from a hook is therefore associated with that context unless the script overrides it.

## Example

Distinguish your own nickname changes from other people's changes while supplying replacement output:

```text
on ^channel_nick * (chan, oldnick, newnick) {
    if (newnick == servernick()) {
        xecho -b You have changed nicks to $newnick;
    } else if (iscurchan($chan)) {
        xecho -b $oldnick has changed nicks to $newnick;
    } else {
        xecho -b $oldnick has changed nicks to $newnick on $chan;
    };
};
```

Your own change can print once per shared channel too. Use `on nickname` instead when one notification per nickname change is desired, and ensure channel hooks allow that event to run.

## History

This event already existed in ircII.

## Source references

- [source/parse.c](../../source/parse.c): `p_nick` updates your server nickname, invokes per-channel hooks, conditionally invokes `nickname`, and then renames channel nicknames.
- [source/names.c](../../source/names.c): `walk_channels` finds channels containing the old nickname; `rename_nick` updates their nickname lists.
- [source/server.c](../../source/server.c): `accept_server_nickname` records your accepted nickname.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
