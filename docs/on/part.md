# on part

## Synopsis

```text
/on [<noise>]part "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the server reports someone, including you, leaving a channel through an IRC `part` message. It does not report quitting IRC or being kicked; see [on signoff](signoff.md), [on channel_signoff](channel_signoff.md), and [on kick](kick.md).

Sending [part](../commands/part.md) does not generate the event before the server reports the departure. Use standard [on](../commands/on.md) matching and noise controls. The pattern matches nickname, channel, userhost, and optional reason together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Nickname of the person leaving. |
| `$1` | Channel being left. |
| `$2` | Person's `user@host` from the message prefix, when present. |
| `$3-` | Part reason, when the server supplies one. |

The prefix userhost can be empty and is not fetched by this handler. The reason depends on the server message and may be absent or explicitly empty. A missing channel parameter is treated as malformed. The handler joins trailing reason parameters into text before invoking the event.

## Default behavior and timing

If the hook permits default handling, EPIC displays `nickname has left channel channel`. When a reason parameter is present, including an empty one, it displays `nickname has left channel channel because (reason)` instead. Output includes EPIC's usual message prefix and uses the channel's context at the `part` message level.

A suppressing hook, such as a serial-zero `^part` hook, prevents the built-in line. It does not reverse the server-side departure or prevent local membership removal.

The event runs before EPIC removes the departing nickname from its cache. For your own part, EPIC removes the channel record afterward, generating [on channel_lost](channel_lost.md) when that record exists. Other people's parts remove only their membership in that channel. Queries during the hook can therefore still find the departing member or channel record.

The hook does not automatically rejoin, reply, or send another IRC request. Any script-generated join is a new request subject to server rules.

## Example

Distinguish your own departure from another user's:

```text
on ^part "*" (nick, channel, userhost, reason) {
    if (nick == servernick()) {
        xecho -b You have left channel $channel;
    } else {
        xecho -b $nick ($userhost) has left channel $channel;
    };
    if (strlen($reason)) {
        xecho -b Reason: $reason;
    };
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_part` assembles the optional reason, invokes the hook and display, and removes membership afterward; `BreakArgs` extracts prefix userhost information.
- [source/names.c](../../source/names.c): `remove_channel` generates `channel_lost` before deleting your channel record; `remove_from_channel` deletes another member's cached entry.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
