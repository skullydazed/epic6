# set status_chanop

## Synopsis

```text
/set status_chanop [<text>]
/set -status_chanop
```

Default: `@`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the channel-operator marker text rather than restoring the default.

## Description

Sets the string displayed for `%@` in [status_format](status_format.md) when EPIC's cached state says you are a channel operator on that window's current channel. If the window has no server or current channel, the field is empty.

The marker is inserted directly. `%@` has no special substitution meaning inside `status_chanop`, and other status percent fields are not recursively interpreted there. The global or per-window status format must contain `%@` for the field to appear.

The same `%@` field can instead display [status_halfop](status_halfop.md) when the operator-marker branch does not return text and EPIC records you as a half-operator. Operator text takes precedence when both states are present and the operator marker is set. Unsetting this setting therefore does not necessarily hide the shared field if the half-operator branch applies.

## Effects and limitations

The marker uses each window's channel state and is not restricted to the selected input window by [show_status_all](show_status_all.md). Changing the text rebuilds status formats and requests updates. It does not grant or remove channel privileges, request fresh mode information, or send an IRC command. The actual privilege meanings and mode updates come from the server.

Ordinary command parsing can expand dollar expressions while assigning the text. If [status_does_expandos](status_does_expandos.md) is enabled, the assembled status line can also expand dollar expressions afterward; that is separate from the marker's direct insertion.

## Example

Customize the operator indicator:

```text
/set status_chanop @
```

Use `/set -status_chanop` to remove its text. Your actual channel privileges remain unchanged, and a half-operator marker can still appear where applicable.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and `@` default.
- [source/status.c](../../source/status.c): `%@` maps to `status_chanop`, which checks the operator branch before the half-operator fallback; optional final expando processing is separate.
- [source/names.c](../../source/names.c): `get_channel_oper` and `get_channel_halfop` provide cached channel privilege state.
