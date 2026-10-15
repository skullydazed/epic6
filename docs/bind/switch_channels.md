# switch_channels

## Synopsis

```text
/bind <key> switch_channels
```

## Description

Cycle through eligible channels on the current window's server, making the least recently selected channel current in this window. Selecting a channel updates its selection order, so repeated invocations rotate through eligible channels rather than sorting their names.

Eligibility depends on [switch_channels_between_windows](../set/switch_channels_between_windows.md):

- When off, only channels already associated with the current window are considered.
- When on, channels associated with other windows on the same server can also be considered, except for another window's current channel. Selecting one moves its window association to the current window.

The setting defaults to on in EPIC6. If you want this binding to cycle only within a window, turn it off. With fewer than two eligible channels, the action makes no change.

This built-in action changes the current channel and triggers [on switch_channels](../on/switch_channels.md) with the window number, old channel, and new channel. It does not join or part a channel, move input focus to another window, or send an IRC request. A window's query target takes precedence over its current channel for ordinary outgoing text, so changing the channel does not override an active query.

The action takes no arguments or options; extra text supplied with the binding is ignored. The default binding is Control-X (`^X`), which scripts can change. If Control-X is also used as a prefix for longer bindings, key-sequence resolution can delay its standalone action.

## Example

```text
/set switch_channels_between_windows off
/bind ^X switch_channels
```

With several channels associated with the current window, press Control-X to rotate the current channel among them. Use [window channel](../window/channel.md) to associate or select a particular channel explicitly.

See [bind](../commands/bind.md) for key sequence syntax and [switch_query](switch_query.md) to cycle query targets.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-X binding.
- [source/names.c](../../source/names.c): `switch_channels` selects candidates by `curr_count`; `set_channel_window` changes association, records selection order, and invokes the switch hook.
- [source/window.c](../../source/window.c): `get_window_target` chooses a query before a channel.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): setting registration and default for `switch_channels_between_windows`.
