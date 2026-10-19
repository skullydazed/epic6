# set status_voice

## Synopsis

```text
/set status_voice [<text>]
/set -status_voice
```

Default: `+`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the voice marker rather than restoring the default.

## Description

Sets the status-bar string displayed when EPIC records you as voiced, but not a channel operator, on that window's current channel. If the window has no server or current channel, or the voice condition is not met, the field is empty.

The field is represented by `%=` in [status_format](status_format.md), or a per-window format. The text is inserted directly: `%=` inside `status_voice` is literal rather than a nested substitution. The outer format must contain `%=` for the marker to appear.

Channel-operator status suppresses this voice field even if [status_chanop](status_chanop.md) has been unset. The operator marker is a separate `%@` field, not a replacement automatically inserted at `%=`. Half-operator status alone does not suppress the voice field; a voiced half-operator can show both markers if both fields are included.

## Effects and expansion

Each window uses its own cached channel state without a current-window restriction from [show_status_all](show_status_all.md). Voice privileges and their meaning are server-dependent. Changing this marker rebuilds status formats and requests updates; it does not grant or remove voice, query the server, or send an IRC request.

Ordinary command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can additionally expand afterward.

## Example

Restore the default voice indicator:

```text
/set status_voice +
```

Hide it with `/set -status_voice` without changing channel privileges. A format containing both `%=%@` lets the separate voice and operator/half-operator fields apply their own conditions.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and `+` default.
- [source/status.c](../../source/status.c): `%=` maps to `status_voice`, which checks voice and excludes operators; `%@` is handled separately.
- [source/names.c](../../source/names.c): `get_channel_voice` and `get_channel_oper` return cached per-channel privilege state.
