# set mode_stripper

## Synopsis

```text
/set mode_stripper [on|off|toggle]
```

Default: `off`. With no argument, [set](../commands/set.md) displays the current value.

## Description

When on, enables the [on mode_stripped](../on/mode_stripped.md) hook. It breaks complex incoming mode changes into single signed mode letters, making each change easier for scripts to process and act upon. For example, a channel change `+oo alice bob` produces separate `+o alice` and `+o bob` events when the server identifies `o` as a membership mode.

This supplements [on mode](../on/mode.md), which still receives the combined change. It does not remove letters from the message or change which modes the server applies. It handles incoming channel and user `MODE` messages, not mode-query numeric replies. Changing the setting affects later messages; it does not replay earlier changes or send a request.

## Event behavior

Each stripped event supplies the sender, target, signed mode letter, and optional channel-mode argument. Letters are reported in their original order, with `+` assumed until a sign is encountered. User-mode events have no per-letter argument. Channel-mode argument assignment depends on the server's `prefix` and `chanmodes` classifications; unknown modes or incorrect classifications can misalign later arguments. See the hook documentation for its full parameter rules.

Stripped events run before the combined `mode` event, and EPIC updates its mode cache afterward. Queries made inside either handler can still see the previous cached state.

There is no built-in display for a stripped event. Its handler's suppression result does not stop the combined event, its normal display, subsequent stripped events, or the cache update. To replace the ordinary mode-change display, handle and suppress `mode` separately. Enabling this setting alone does not change the default display.

## Example

```text
/set mode_stripper on
/on ^mode_stripped "% % +o *" (sender, channel, change, nick) { echo $sender granted operator status to $nick on $channel; }
```

This reports individual `+o` changes on servers that classify `o` as a membership mode. The regular combined mode-change display remains. Disable future stripped events with `/set mode_stripper off`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and off default.
- [source/parse.c](../../source/parse.c): `p_mode` conditionally calls `strip_modes`, then runs `mode` and updates cached state; stripped hook return values are ignored.
- [source/names.c](../../source/names.c): `chanmodetype` determines channel-mode argument consumption.
- [source/hook.c](../../source/hook.c): `mode_stripped` hook registration.
