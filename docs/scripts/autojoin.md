# autojoin

## Synopsis

The script requires the `addset` support normally supplied by the default `builtins` startup script. If that support is absent, load `addset` first.

```text
/load autojoin
/set autocreate_split_channels [on|off|toggle]
/join #chan1[,#chan2...] [key1[,key2...]]
```

## Description

The `autojoin` script reads permanent channels from `~/.irc/channels`, joins them when a server in the corresponding group becomes active, and gives each channel its own named window. These are script-provided behaviors: loading the script defines a `join` alias and installs connection, join, and window-deletion hooks.

The file contains whitespace-separated records:

```text
<channel> <winnum> <group> [key]
```

For example:

```text
#epic 2 efnet
#test321 3 efnet test.8
```

Use the server's configured group name, not its hostname unless that is also its group. Channel names are stored in lowercase. The optional key is stored as plain text in this file. Lines without a third field are skipped; the loader does not implement a comment-line syntax or validate window numbers.

The file is read when the script loads, not on every connection. An unreadable or absent file is silently ignored. The script does not save channels back to the file. To reread it after editing, use the script's `_loadchannels` alias. A successful read replaces the in-memory permanent-channel list; an unsuccessful open leaves that list unchanged.

For each matching record, the script sends a join request when the server enters its `active` state. A successful self-join triggers window placement. The server decides whether the join succeeds, including key, invitation, ban, and channel-limit checks. Loading the script after a connection is already active does not itself trigger the permanent-channel joins; they occur on a later transition to `active`.

## Windows and joining

A channel window is identified by a generated name encoding its server group and channel. If that named window already exists, the script associates it with the current server and channel. Otherwise it creates and names a window, associates the channel, and applies the configured window number. An occupied number exchanges refnums with the existing window; it does not select that existing window for reuse. Window creation remains subject to normal screen-space and window-operation restrictions.

`autocreate_split_channels` is a script-defined boolean setting, initialized to `off` whenever this script loads. With `off`, the script uses [window new_hide](../window/new_hide.md) and swap operations; automatic joins normally leave the new channel window hidden and restore the previous window. With `on`, it uses [window new](../window/new.md), creating a visible split when space permits. This setting affects new windows, not existing ones.

The replacement `/join` accepts comma-separated channels and corresponding comma-separated keys. It lowercases channels and adds `#` when a name lacks a recognized channel prefix. If no explicit key is supplied, it looks up the permanent-channel key for the current group. If the key is still empty, it supplies `*`. Channels beginning with `!` bypass the pre-join window creation and use the built-in join directly.

If a requested channel is already joined, the alias attempts [window swap](../window/swap.md) with its window and stops processing the remaining channel list. Swap requires a hidden target and swappable windows; an already visible target produces a diagnostic rather than selecting it. `/join` without arguments prints usage instead of the built-in channel listing. Use `//join` to bypass the alias when needed.

When a window is being killed, the script sends [part](../commands/part.md) for every channel associated with that window on its server. This differs from the usual behavior of moving channels to another window. It applies to all such windows, not just permanent-channel windows. The part requests are sent from the `window_beforekill` hook, before the window is deleted.

## Reconnection scope

Permanent channels are requested again on subsequent connections to the matching group. The script does not establish or retry connections itself. Its additional recovery code reads an older `reconn` channel/key structure; the current [reconnect](reconnect.md) script uses a different `auto_reconnect` structure and handles its own channel restoration. Do not rely on this older integration to restore arbitrary channels or their window placement. Group-based names also mean simultaneous connections in one group can compete for the same named windows.

## History

This script first appeared in EPIC5-0.3.1.

## Source references

- [script/autojoin](../../script/autojoin): file loader, aliases, setting, and hooks.
- [script/reconnect](../../script/reconnect): current reconnection data and channel restoration.
- [script/builtins](../../script/builtins) and [script/addset](../../script/addset): normally loaded support for script-defined settings.
- [source/server.c](../../source/server.c): server-state hook dispatch.
- [source/parse.c](../../source/parse.c): self-join processing and join hook.
- [source/window.c](../../source/window.c): window creation, channel assignment, swapping, numbering, and pre-deletion hook.
