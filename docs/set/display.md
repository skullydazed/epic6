# set display

## Synopsis

```text
/set display [on|off|toggle]
```

Default: `on`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

Controls ordinary client output that honors display permission, including normal informational messages and many built-in default displays. Turning it off suppresses that output without stopping command execution, server-message processing, or state updates. It is not a complete terminal mute switch.

[Echo](../commands/echo.md) explicitly prints even when display permission is off. [Xecho](../commands/xecho.md) also normally forces its output, while `xecho -s` honors current suppression. Ordinary `yell` diagnostics and privileged diagnostics can also print despite this setting. Screen redraws and status handling have their own controls.

Output suppressed before reaching the normal output pipeline is not simply hidden until later: it is not queued for redisplay and does not reach that pipeline's usual logging, lastlog, and scrollback insertion. Explicit output paths that bypass suppression can still be recorded and displayed normally.

## Temporary suppression and script loading

EPIC tracks a temporary display-permission state in addition to this persistent setting. Quiet command prefixes and hook noise modes can suppress ordinary output temporarily. The command parser normally restores the previous temporary state after a statement, but if the statement changes `display`, it adopts the new setting instead.

Loading a script temporarily disables ordinary output and status updates; it does not simply change the stored `display` setting to off. At the end of loading, EPIC normally restores the previous display permission when the stored setting is on. Explicitly setting `display off` in a script can leave suppression in effect afterward. Consequently, finishing a load does not unconditionally enable output.

Setting `display on` in a loaded script can enable ordinary output for its subsequent execution, subject to surrounding temporary suppression. Use explicit `echo` for a message that should be printed even when ordinary display is disabled.

Changing this setting sends no IRC request. It does not prevent commands executed while output is suppressed from sending their own requests.

## Example

Suppress ordinary output while retaining explicit script messages:

```text
/set display off
/echo This explicit message is still printed.
/set display on
```

## Source references

- [source/output.c](../../source/output.c): display-permission state, ordinary `put_it`/`say` checks, and diagnostic paths that bypass them.
- [source/commands.c](../../source/commands.c): `parse_statement` preserves or adopts display permission; `load` temporarily suppresses output; `echo` and `xecho` explicitly enable output.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and default on.
