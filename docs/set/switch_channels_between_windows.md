# set switch_channels_between_windows

## Synopsis

```text
/set switch_channels_between_windows [on|off|toggle]
```

Default: `on`. With no argument, [set](../commands/set.md) displays the current value.

## Description

Controls whether the [switch_channels](../bind/switch_channels.md) binding can move noncurrent channels from other windows on the same server into the current window.

When on, the binding considers channels belonging to the current window and eligible channels in other windows on its server. It never takes another window's current channel. This permits moving channels between windows without joining or parting them.

When off, only channels already associated with the current window are considered. This keeps channel cycling within the window, which can be useful when you want channels to stay where you put them.

Among eligible channels, the binding selects the least recently made current. Repeated actions rotate selection order rather than sorting by channel name. With fewer than two eligible channels, the action makes no change.

## Effects and limits

This is a global policy checked when the binding runs. Changing it does not immediately move channels, change the selected window, or redistribute existing channel associations. It does not prevent explicit channel movement with [window channel](../window/channel.md) or other channel-management commands.

Channels on other servers are not candidates. Selecting an eligible channel changes its association and makes it current in the receiving window; it does not move input focus to its former window. The action sends no IRC join or part request. An active query can still take precedence over the current channel as the target for ordinary typed text.

## Example

Keep cycling within each window:

```text
/set switch_channels_between_windows off
```

Then use the `switch_channels` binding, normally Control-X, to rotate among that window's channels. Restore cross-window eligibility with `/set switch_channels_between_windows on`.

## History

This setting first appeared in EPIC4-1.1.12.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and on default.
- [source/names.c](../../source/names.c): `switch_channels` filters by server, window ownership, and current-channel status, then calls `set_channel_window` for the oldest eligible selection.
- [source/keys.c](../../source/keys.c): binding registration and default key.
- [source/window.c](../../source/window.c): query-before-channel target selection.
