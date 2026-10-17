# on kick

## Synopsis

```text
/on [<noise>]kick "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC receives a server report that someone, including you, was [kicked](../commands/kick.md) from a channel. Sending a kick request does not generate the event before the server reports the removal.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the victim, kicker, channel, and comment together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Nickname removed from the channel. |
| `$1` | Sender who performed the kick, normally a nickname. |
| `$2` | Channel where the kick occurred. |
| `$3-` | Kick comment. |

When the server omits the comment parameter, EPIC supplies `(no comment)`. An explicitly empty comment remains empty. Missing channel or victim parameters are rejected as malformed messages. The server supplies the reason; the hook does not establish why it chose to permit the kick.

## Default behavior and timing

If the hook permits default handling, EPIC displays one of these lines with its usual message prefix:

- `You have been kicked off channel channel by kicker (comment)` for your own removal.
- `victim has been kicked off channel channel by kicker (comment)` otherwise.

A suppressing hook, such as a serial-zero `^kick` hook, prevents the default line. It does not reverse the server-side kick or prevent local cache removal.

The hook runs before EPIC removes membership information. For another person's kick, their nickname and cached userhost are still available during the handler; afterward EPIC removes them from that channel. For your own kick, the channel record remains available during the handler, then EPIC removes it and generates [on channel_lost](channel_lost.md).

For your own removal, EPIC temporarily makes the channel's owning window the global current window and directs output there. It restores the previous global current window before channel removal. This is an internal current-window change, not an input-focus selection. Other victims' kicks use normal channel routing. Both paths use the `kick` message level.

If EPIC receives your kick for a channel it does not believe you are on and has no associated window, it prints a diagnostic and returns without this hook. The handler does not automatically rejoin. Any rejoin performed by a script is a new server request and is subject to channel rules.

## Example

Distinguish your own removal from another user's:

```text
on ^kick "*" (victim, kicker, channel, comment) {
    if (victim == servernick()) {
        xecho -b You have been kicked from $channel by $kicker ($comment);
    } else {
        xecho -b $victim has been kicked from $channel by $kicker ($comment);
    };
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_kick` validates parameters, selects the self-kick or other-victim branch, invokes the hook, and removes membership afterward.
- [source/names.c](../../source/names.c): `remove_channel` generates `channel_lost` before deleting your channel record; `remove_from_channel` deletes another victim's membership.
- [source/window.c](../../source/window.c): `make_window_current_informally` changes the global current window without selecting input focus.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
