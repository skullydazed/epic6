# set beep

## Synopsis

```text
/set beep [on|off|toggle]
```

Default: `on`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

Controls EPIC's terminal bell routine. When off, calls through that routine do not emit a terminal bell, including the [beep](../commands/beep.md) command, bells encountered during normal full-screen output, and [window beep_always](../window/beep_always.md) notifications. When on, those paths can request a bell, subject to other output controls.

This setting does not convert Control-G (`^G`, BEL) into a visible `G`. Normal display normalization preserves BEL unless unprintable characters are being stripped; the terminal output path consumes it as a bell request rather than displaying a glyph. Turning `beep` off suppresses that request without inserting replacement text.

The separate historical `beep_on_msg` and `beep_when_away` settings are not present in the current built-in variable table. Scripts can implement their own notification policies, but a script calling the built-in `beep` command still uses this setting.

## Limits and interactions

The terminal routine also respects an internal bell-permission flag, which EPIC disables while repainting window contents. Turning `beep` on therefore does not make every redraw ring again. Bell characters removed by output mangling or output suppression cannot reach the normal display bell path.

For hidden windows, `beep_always` can request a bell and produce a local `Beep in window N` notification. Turning this global setting off silences the terminal-bell call; it does not itself cancel the hidden-window notification text.

EPIC emits the terminal's `bel` capability. Whether this becomes a sound, visual flash, notification, or nothing depends on the terminal emulator and its configuration. In dumb mode, ordinary text is printed directly to standard output rather than through the full-screen bell parser, so embedded BEL characters in that text are not filtered by this setting. The explicit `beep` command still uses the gated terminal routine.

Changing the setting affects subsequent bell requests and sends no IRC request.

## Example

Disable EPIC's terminal-bell routine:

```text
/set beep off
/beep
```

The second command does not emit a bell through that routine. Re-enable it with `/set beep on`.

## Source references

- [source/term.c](../../source/term.c): `term_beep` checks `beep` and internal bell permission before emitting `bel`.
- [source/screen.c](../../source/screen.c): normalizes BEL, handles display bell requests and hidden-window notifications, suppresses bells during repainting, and has a direct-output dumb-mode path.
- [source/commands.c](../../source/commands.c): `beepcmd` calls `term_beep`.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and default on.
