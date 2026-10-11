# channel function

## Synopsis

```text
$channel([channel-name])
```

## Technical

`channel()` returns extended information about nicknames in EPIC's cached member list for one channel. The result is a sorted, space-separated nickname list, with two status characters prepended to each nickname. It sends no IRC request.

If no channel name is supplied, or the name is `*`, the current window's current channel name is used. Lookup uses the current execution server (`from_server`), with primary-server fallback when that context has no server. In an [`on`](../commands/on.md) handler, the default channel need not be the event's channel; pass its name explicitly when needed. There is no server or window argument. Only the first argument word is used, and later words are ignored.

Argument parsing honors [`xdebug dword`](../commands/xdebug.md). Channel lookup and nickname-list ordering use the server's case mapping as configured for the cached channel. The function reports local knowledge; an untracked channel or a channel with no cached nicknames returns the empty string.

## Returns

Each entry has the form `OVnickname`:

| Position | Character | Meaning |
| --- | --- | --- |
| `O` | `@` | Cached channel operator status |
| `O` | `%` | Cached half-operator status when not an operator |
| `O` | `.` | Neither operator nor known half-operator |
| `V` | `+` | Cached voice status |
| `V` | `?` | Voice status unknown |
| `V` | `.` | No cached voice status |

The first position does not return `?` for unknown operator status. These are fixed output markers, not a complete reproduction of the server's advertised prefix hierarchy: other privilege levels are not represented. A nickname can have both an operator marker and a voice marker.

## Practical

This function is useful for scripts that format or display channel members with privileges. EPIC normally has member records only for channels it has joined. Before initial synchronization finishes, or when the server supplies incomplete prefix information, the cached list or statuses can be incomplete. The voice `?` marker reflects that uncertainty; a dot is not proof that the server has supplied every possible privilege state.

Use [`chanusers()`](chanusers.md) for the nickname list without these two-character annotations, or [`names`](../commands/names.md) to request the server's membership information asynchronously. Do not confuse this function with the [`channel` command](../commands/channel.md), which joins channels.

## Examples

```text
$channel(#example)
$channel(*)
$channel()
```

A result such as `@+Alice ..Bob %+Carol` means Alice is cached as an operator with voice, Bob has neither annotation, and Carol is a half-operator with voice. Actual names and order depend on the cached channel list.

## Source

[source/functions.c](../../source/functions.c) registers `channel` and implements `function_channel()`. [source/names.c](../../source/names.c) implements `scan_channel()`, channel lookup, member status tracking, and the sorted nickname table. [include/words.h](../../include/words.h) defines argument parsing.
