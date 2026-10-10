# join

Join IRC channels, select an already joined channel in the current window, or display locally known channels.

## Synopsis

```text
/join
/join channel-list [key-list]
/join -invite [key]
/join 0
```

## Description

The C built-in `join` is identical to [channel](channel.md). With no arguments, it displays locally known joined channels; it does not join the last invited channel. With arguments, it uses the current window's `rejoin` operation. See `channel` for full window-placement and current-channel behavior.

The first argument is a comma-separated channel list, and the optional second is a key list forwarded unchanged to the server. For channels not already joined, EPIC sends a `JOIN` request and records their destination window. Channels already joined on the current window's server are moved into that window and made current without another request.

Use an actual channel prefix accepted by the server, commonly `#`, `&`, `!`, or `+`. The built-in does not prepend `#` to a bare name. EPIC uses the server's `CHANTYPES` information, falling back to those prefixes. Invalid entries are diagnosed and skipped; server naming and admission rules may impose further restrictions.

`-invite` substitutes the last invitation recorded for the current window's server; `-i` is also accepted. Without a recorded invitation, that entry is skipped with a diagnostic. `0` sends `JOIN 0`, conventionally requesting departure from all channels on the current server.

## Script alias differences

Scripts can override `join`. The alias in [script/2.8script](../../script/2.8script), when loaded, supplies the familiar convenience behavior: no arguments joins the last invited channel, bare names receive a `#` prefix, and comma-separated requests are split recursively. These are script features rather than built-in behavior. Use `//join` to bypass a script alias and invoke the C built-in.

The script's comma splitting is not equivalent to forwarding a channel list with a matching key list. Use the built-in or join keyed channels separately when keys matter.

## Restrictions

Requests require a registered connection in the current command context; otherwise EPIC reports that you are not connected. Server modes, keys, bans, invitations, and membership limits determine whether joins succeed. Replies and errors arrive asynchronously. Listing existing local channel state needs no new server query.

Keys are not realigned when invalid or already joined entries are omitted from the outgoing channel list, so separate keyed requests avoid positional mismatches. The built-in reads only the channel argument and optional key argument. It is always registered, with no special build requirements.

## Examples

Invoke the built-in explicitly to join one channel or several:

```text
//join #channel
//join #one,#two
//join #private secretkey
```

Join the last invited channel, list local channel state, or request departure from all channels:

```text
//join -invite
//join
//join 0
```

## Source

Both built-in names are registered to `e_channel` in [source/commands.c](../../source/commands.c), with no command-name distinction. Parsing, keys, special forms, and destination windows are handled in [source/window.c](../../source/window.c). Channel listing and moves are in [source/names.c](../../source/names.c), and channel-prefix recognition is in [source/parse.c](../../source/parse.c).
