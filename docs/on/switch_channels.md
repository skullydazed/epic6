# on switch_channels

## Synopsis

```text
/on [<noise>]switch_channels "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when a window's current channel is switched. This includes selecting a channel, making a newly joined channel current, and replacing a current channel that is removed or moved to another window. It is not limited to the [switch_channels](../bind/switch_channels.md) key binding.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the window refnum, old channel, and new channel together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | User-visible refnum of the window in which the switch occurred. |
| `$1` | Previous current channel, or `0` if there was none. |
| `$2` | New current channel, or `0` if no channel remains. |

## Effects and ordering

The channel association and current-channel selection have already changed when this hook runs. Its suppression result is ignored: it cannot cancel the switch, and the event has no separate built-in output.

Moving a channel that is current in its old window and making it current in another window can produce two events: first for the old window's replacement channel, then for the destination window's newly selected channel. Selecting a channel that is already current in the same window returns without another event.

The window in `$0` need not be the window with input focus. This event concerns that window's current channel, not its query target; an active query can still take precedence for ordinary outgoing text. Switching input focus between windows is a separate [on switch_windows](switch_windows.md) event.

The hook itself sends no IRC request. A surrounding operation such as joining or parting can involve server traffic, while a local channel selection does not join or part the channel.

## Example

Observe channel selection changes:

```text
on -switch_channels "*" (win, oldchan, newchan) {
    echo Window $win changed channel from $oldchan to $newchan;
};
```

## Source references

- [source/names.c](../../source/names.c): `set_channel_window` updates channel associations and invokes the hook; `destroy_channel` invokes it after selecting a replacement; `add_channel` selects a newly joined channel.
- [source/window.c](../../source/window.c): window channel and query target lookup.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
