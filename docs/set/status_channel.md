# set status_channel

## Synopsis

```text
/set status_channel [<text>]
/set -status_channel
```

Default: ` %C`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes this channel field's format rather than restoring the default.

## Description

Sets the format of the current channel name in each window's status bar. If that window has no server or no current channel, the field is empty. This is not restricted to the selected input window; visible noncurrent windows can show their own current channels.

The field is represented by `%C` in [status_format](status_format.md), or a per-window status format. The first `%C` inside `status_channel` is replaced by the window's channel name before the result is inserted into the outer status line. Other status percent fields are not recursively expanded there. If you omit `%C`, the configured text can still appear when the window has a channel, but it will not include the name.

Changing the setting rebuilds status formats and requests status updates. It does not join or switch channels, change the current window, or send an IRC request.

## Related formatting

[Hide_private_channels](hide_private_channels.md) can replace a private channel's name with `*private*`. A positive [channel_name_width](channel_name_width.md) then truncates the substituted name to that many bytes; it does not pad short names. EPIC also applies its normal channel-type presentation helper before inserting the name.

Dollar expressions can be expanded when assigning the setting. If [status_does_expandos](status_does_expandos.md) is enabled, the assembled status line can additionally expand dollar expressions afterward. This is separate from the `%C` substitution.

## Example

Customize the channel field:

```text
/set status_channel Chan: %C
```

For a window whose current channel is `#example`, this normally supplies `Chan: #example`. It appears only where the window's status format contains `%C`. Hide the field with `/set -status_channel`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default text.
- [source/status.c](../../source/status.c): `%C` mapping, `convert_sub_format`, channel selection, privacy substitution, width truncation, and optional final expando pass.
- [source/window.c](../../source/window.c): `get_window_echannel` supplies the window's current channel.
- [source/names.c](../../source/names.c): private-channel state and `check_channel_type` presentation.
