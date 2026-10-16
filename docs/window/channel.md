# window channel

## Synopsis

```text
/window [<window>] channel
/window [<window>] channel <channel>[,<channel>...]
/window [<window>] channel "<channel>[,<channel>...] <key>[,<key>...]"
/window [<window>] channel -invite
/window [<window>] channel "-invite <key>"
```

## Description

Associate channels with the selected window and make them its current channel as they are selected. With no window selector, operate on the current window. Channel output follows that association instead of being displayed in another window.

If you are already on a listed channel on the window's server, EPIC moves its association to this window and selects it as current without sending a new join. Otherwise it records a pending window association and sends an IRC `join` request. The channel becomes available only if the server accepts the join; invalid keys, invitations, bans, limits, or other server policy can prevent it.

A window can contain several channels. Use [switch_channels](../bind/switch_channels.md) to cycle among them. A query takes precedence over the current channel for ordinary outgoing input, so selecting a channel does not override an active [query](../commands/query.md).

## Arguments

Channel names are comma-separated. Keys, when needed, are a corresponding comma-separated list separated from the channel list by a space. Quote the entire channel-and-key argument so both lists reach this operation together; unquoted words after the channel token are interpreted as further window operations.

`-invite` selects the last channel to which you were invited on this window's server. If none is recorded, EPIC reports `You have not been invited to a channel!`. A key can be included in the quoted form. The implementation recognizes this option by its first two characters, so `-i` is also accepted.

With no channel argument, report the current channel and the channels associated with the window, or `There are no channels in this window`. `/window -channel` is a no-op. The value `0` does not detach or part a channel; it is rejected as an invalid channel name.

## Output and limitations

The window must have an associated server; otherwise EPIC reports `This window is not connected to a server.` A live connection is needed to complete new joins. Operations apply only to this server's channels.

An invalid channel name reports `CHANNEL: name is not a valid channel name`, and processing continues with remaining names. Selecting an already joined channel reports `You are now talking to channel name`. Local association changes update channel status and invoke [on switch_channels](../on/switch_channels.md) when selecting a new current channel.

Moving a channel does not move its previously displayed output or part it. This operation does not create a new window. Server replies and hooks determine subsequent join messages and errors.

## Examples

```text
/window channel #example
/window 3 channel #one,#two
/window channel "#private secretkey"
/window channel "#one,#two key1,key2"
/window channel -invite
```

Use [window](../commands/window.md) for window selection syntax and [window add](add.md) for nickname-based output routing.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_channel` parses lists and invitation shortcuts, records waiting channels, and sends join requests; `get_window_target` gives queries precedence.
- [source/names.c](../../source/names.c): `move_channel_to_window`, `set_channel_window`, and channel creation implement association, current-channel selection, hooks, and pending-window claims.
- [source/screen.c](../../source/screen.c): channel output routing uses the channel's associated window.
