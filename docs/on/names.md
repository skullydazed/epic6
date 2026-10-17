# on names

## Synopsis

```text
/on [<noise>]names "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for a server names-reply numeric `353` that reaches EPIC's names-display stage. This includes replies to [names](../commands/names.md) and the initial names replies received while joining a channel. One reply can contain only part of the channel's users, so a listing can generate multiple events.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the channel and nickname-list text together. There are no event-specific options. The server determines which users are visible and which status prefixes or other negotiated fields appear in the list.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Channel name. |
| `$1-` | Nickname-list text supplied by this reply, including any status prefixes. |

The channel's public/private/secret marker is not included in these hook arguments. Missing marker or channel parameters are malformed; absent nickname-list text is treated as empty. The event is not an end-of-list or full-cache-completion notification. See numeric `366` for end-of-names and [on channel_sync](channel_sync.md) for completion of initial synchronization.

## Ordering and suppression

During initial synchronization, EPIC caches the names before numeric display hooks and before `on names`. The `353` numeric hook and [on numeric](numeric.md) can suppress the display stage before this event runs. Thus `on names` is not guaranteed for every received `353`, but suppressing this event does not undo names already cached.

A suppressing hook, such as a serial-zero `^names` hook, prevents this reply's built-in names display. It does not cancel the server's listing, remove cached users, or suppress later replies. The event does not itself send an IRC request.

## Default display

While the channel is syncing, if the hook permits default handling and [set show_channel_names](../set/show_channel_names.md) is on (the default), EPIC prints `Users on channel: names` with its usual message prefix. Turning that setting off does not prevent the hook from running.

Outside synchronization, permitted replies display a `Pub:`, `Prv:`, or `Sec:` label for server markers `=`, `*`, or `@`. Other markers have no built-in line in this branch. [Set channel_name_width](../set/channel_name_width.md), default `0`, affects formatting, but the current code uses its padded/truncated format only when the configured width changes; subsequent replies revert to tab-separated formatting. Public channel names can still be shortened with a trailing `>` when they exceed a nonzero width.

Built-in output is routed to the channel at the `other` message level. During synchronization the hook uses that channel context; outside synchronization the code selects the channel display context after the hook.

## Example

Replace the built-in display for each names reply:

```text
on ^names "*" (channel, users) {
    echo Users reported for $channel: $users;
};
```

This works for initial join replies too, provided earlier numeric hooks allow the names-display stage.

## Source references

- [source/numbers.c](../../source/numbers.c): the first numeric `353` branch caches syncing names; numeric hooks run before the later display branch, which invokes `names` and handles both formatting paths; numeric `366` handles end-of-names separately.
- [source/names.c](../../source/names.c): `add_to_channel` caches initial nickname and status information.
- [include/config.h](../../include/config.h): defines `show_channel_names` on and `channel_name_width` zero by default.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
