# channel command

## Synopsis

```text
/channel
/channel -invite
/channel <channel-list> [key-list]
/channel 0
```

## About channels

A channel is a named place where people on an IRC network can talk together. Networks have separate sets of channels. Names commonly begin with `#`; other prefixes, channel lifetimes, name limits, and operator privileges depend on the network. EPIC uses the server's advertised `CHANTYPES` prefixes, falling back to `#`, `&`, `+`, and `!` when that information is unavailable.

Servers can restrict entry with channel modes such as a key/password, invite-only status, membership limits, or bans and exceptions. See [`mode`](mode.md). Creating or joining a channel and receiving operator privileges are server decisions; EPIC's request does not guarantee admission.

## Description

With no arguments, `channel` displays the current window's channel and locally known joined-channel information. It lists channels on the current server, followed by channels on other servers when applicable. If the client knows of no joined channels, it prints `You are not on any channels`. This form does not request a fresh channel list from the server.

With arguments, the command delegates to the current window's `rejoin` operation. The first argument is a comma-separated channel list, and the second is an optional comma-separated key list. Only these two arguments are read. Both use quoted-word parsing, although channel names accepted by servers normally cannot contain spaces.

For channels not already joined, EPIC records a destination window and sends `JOIN channel-list`, with the supplied key text appended when present. The request requires a registered connection in the current command context; otherwise it reports `You are not connected to a server.` Replies and errors arrive asynchronously.

Channels already joined on the current window's server are moved into that window and made current, without another JOIN request. EPIC reports `You are now talking to channel ...`. New channels become current in their destination windows as their joins are processed. With several channels, the final current channel can therefore depend on the processing order; there is no guarantee that none will become current.

Destination selection uses the window/server context and pending channel requests. Normally a new request targets the current window when it is on the request's server; otherwise an available window on that server is selected. Scripts can override placement through the channel-claim hook when a join is processed. See [`window`](window.md).

## Options and special forms

`-invite` substitutes the last channel invitation recorded for the current window's server. The test accepts any list entry beginning with `-i`, case-insensitively, so `-i` also works. If no invitation is recorded, it prints `You have not been invited to a channel!` and skips that entry.

The special entry `0` sends `JOIN 0`, conventionally requesting departure from all channels on the current server. Other entries in the same list are still processed. Server behavior determines whether this request is supported.

A channel name whose first character fails EPIC's channel-prefix check produces `CHANNEL: name is not a valid channel name` and is skipped. This check does not validate every server-side naming rule. Other list entries can still be requested.

## Keys and restrictions

Key-list handling is server-dependent. EPIC forwards the key text unchanged and does not realign it when channel entries are skipped or already joined. Joining keyed channels separately avoids mismatched positions when some entries are removed from the outgoing channel list.

The command is available in every build. It has no special configure-time requirements. Server modes, channel limits, name rules, and invitation policies can reject a request even when the client accepts its syntax.

## Examples

To join a channel, or ask the server to create it if appropriate:

```text
/channel #irchelp
```

To join a channel requiring a key:

```text
/channel #blah foobar
```

To join several channels:

```text
/channel #foo,#bar,&blah
```

To join the last invited channel, display joined channels, or request departure from all channels:

```text
/channel -invite
/channel
/channel 0
```

## Related commands

The C built-ins `channel` and [`join`](join.md) use the same handler, with no command-name branch distinguishing their behavior. Scripts can override either name. `channel` is a front end for the [`window`](window.md) `rejoin` operation.

## Source

See [source/commands.c](../../source/commands.c) for the registrations and `e_channel()`. [source/window.c](../../source/window.c) implements `windowcmd_rejoin()` and pending window assignments. [source/names.c](../../source/names.c) implements local listing, channel moves, join placement, and current-channel selection; [source/parse.c](../../source/parse.c) implements `is_channel()` and server-advertised prefix handling.
