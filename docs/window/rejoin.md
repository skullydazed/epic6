# window rejoin

## Synopsis

```text
/window [<window>] rejoin <channel>[,<channel>...] [<key>[,<key>...]]
```

## Description

Join several channels and arrange their window associations. This is the built-in backend used by [channel](../commands/channel.md), and is useful in an [on connect](../on/connect.md) script when reconnecting. With no window selector, use the current window; the `/window` dispatcher uses the selected window's server for this operation.

For a channel you have already joined on that server, EPIC moves its local channel association to the selected window and reports `You are now talking to channel <channel>`. It does not part and rejoin that channel. A current query can still take precedence over the channel for ordinary input.

For channels not yet joined, EPIC records a pending window association, collects the channel names, and sends one `JOIN` request, optionally with the supplied key list. The server decides whether joins succeed, including key, invite, ban, limit, and channel-name restrictions. A recorded association does not itself establish membership. Invisible windows can own channel associations without being shown.

## Pending-association limitation

This operation is intended to preserve existing waiting-window associations, but the current implementation checks the **selected window's** waiting list while iterating other windows, rather than checking each candidate's list. If the selected window already waits for the channel, the first window found on the operation's server can become its new pending owner. Otherwise it normally chooses the selected window, even when another window was waiting for the channel.

Recording that new pending owner removes competing waiting entries on the same server. Do not rely on `rejoin` to preserve every preexisting channel-to-window plan. When called internally by `channel` with a server context different from the current window's server, the helper's fallback can choose another window on that server.

## Arguments and special channels

The channel list and optional key list are **separate arguments**, each accepting double quotes. Use commas within each list; do not quote both lists together as one argument. Keys are forwarded unchanged alongside the filtered list of channels needing a join. Invalid names, already joined channels, or special entries are omitted from that list without removing corresponding keys, so mixing them with keyed new joins can misalign keys. Submit keyed joins separately when necessary.

`-invite` (any token beginning with `-i`, case-insensitively) uses the selected window's server's last invited channel. If none exists, it reports `You have not been invited to a channel!` and skips that entry. `0` sends `JOIN 0`, conventionally requesting departure from all channels on the server; server behavior determines the result.

A locally invalid channel reports `CHANNEL: <channel> is not a valid channel name` and is skipped. A missing channel argument reports `REJOIN: Must provide a channel argument`. An unregistered or absent server reports `You are not connected to a server.` No join is sent in those latter cases.

The handler consumes a channel argument and then a possible key argument. To follow it with more window operations when there are no keys, provide an empty quoted key argument so the next operation is not consumed as keys. `/window -rejoin` is a no-op. Failures and skipped entries do not inherently stop subsequent window operations.

## Examples

```text
/window 3 rejoin #chat,#help
/window 3 rejoin #secret key123
/window rejoin -invite
/window 3 rejoin #chat "" describe
```

Join or locally move channels to window 3, request a keyed join, join the last invited channel, or follow a rejoin with a window description. Pending ownership is subject to the limitation above.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_rejoin` parses separate lists, chooses owners, handles special entries, and sends `JOIN`; `add_waiting_channel` removes competing pending claims; `windowcmd` supplies the selected server context.
- [source/names.c](../../source/names.c): `move_channel_to_window` changes existing channel ownership without sending a join.
- [source/commands.c](../../source/commands.c): `e_channel` delegates to `windowcmd_rejoin`.
