# set status_away

## Synopsis

```text
/set status_away [<text>]
/set -status_away
```

Default: ` (Away)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the indicator text rather than restoring the default.

## Description

Sets the string displayed for `%A` in [status_format](status_format.md) when the window's server has a stored away message. When there is no such message, the field is empty. The field represents the client's stored away-message state for that server, not the message text itself.

`%A` must be present in the global or per-window status format for the indicator to appear. It has no special substitution meaning inside `status_away`; the setting supplies the indicator text directly. It does not automatically insert an away reason or recursively interpret status percent fields.

With exactly one connected server and [show_status_all](show_status_all.md) off, the indicator is restricted to the input window of each screen. With multiple connected servers, that restriction does not apply; each window uses its own server's away-message state. A window with no server supplies an empty field.

Changing this setting rebuilds status formats and requests status updates. It does not mark you away or back, send an IRC request, or change server state. Use [away](../commands/away.md) to change your away message.

## Expansion

Ordinary command parsing can expand dollar expressions while assigning the value. If [status_does_expandos](status_does_expandos.md) is enabled, the assembled status line can also expand dollar expressions afterward. This does not make `%A` inside the indicator a nested status-format directive.

## Example

Customize the away indicator:

```text
/set status_away (Away)
```

This stores `(Away)` without the default leading space. Include any desired separator in your status format. Hide the indicator with `/set -status_away`; this leaves your actual away state unchanged.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, `build_status` callback, and default text.
- [source/status.c](../../source/status.c): `%A` maps to `status_away`, which checks server association, stored away message, and window visibility policy; final status expansion is separate.
- [source/server.c](../../source/server.c): `get_server_away_message` returns the stored per-server message.
