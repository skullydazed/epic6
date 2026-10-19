# set hide_private_channels

## Synopsis

```text
/set hide_private_channels [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

When on, the standard status-bar channel field displays `*private*` instead of the current channel's name when EPIC's cached channel modes include `p` or `s`. When off, it uses the actual channel name. Changing the setting requests status updates for all windows.

The decision uses EPIC's channel mode records for that window's server. A channel not found in those records is not classified as private by this test. The server's mode reporting and the current synchronization state therefore determine what EPIC knows at the time of formatting.

The replacement is made before [channel_name_width](channel_name_width.md) truncation, so a small positive width can shorten `*private*` itself. The surrounding status-channel format can add its own text around the replacement.

## Scope

This setting affects the built-in status channel field only. It does not redact channel names from messages, logs, lastlog, hook arguments, channel functions, or script-generated status text. It also does not change channel membership or send a request to change server channel modes.

It is therefore a local display preference, not a guarantee that the channel name is hidden everywhere on the screen or from other users. If there is no current channel, the field is empty rather than displaying the private marker.

## Example

```text
/set hide_private_channels on
```

The standard status channel field then uses `*private*` for a channel whose cached modes include `p` or `s`. Restore normal name display with:

```text
/set hide_private_channels off
```

## Source references

- [source/status.c](../../source/status.c): `status_channel` selects the replacement before channel-width truncation and status formatting.
- [source/names.c](../../source/names.c): `is_channel_private` tests cached base modes for `p` or `s` and returns false for an unknown channel.
- [source/vars.c](../../source/vars.c): boolean registration and status-update callback.
- [include/config.h](../../include/config.h): default off.
