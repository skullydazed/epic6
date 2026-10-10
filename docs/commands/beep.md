# beep command

## Synopsis

```text
/beep
```

## Description

This command requests a bell from your terminal emulator. It is sometimes simpler than using [`echo`](echo.md) to output control-G characters.

The command is strictly local; it does not send beeps to other users or contact an IRC server. It produces no text output. There are no options or required arguments, and any supplied arguments are ignored. It is available in every build.

If `set beep` is off, the command has no effect. The configured default is on. EPIC also temporarily suppresses bells while repainting window contents, so redrawing old text does not repeatedly ring the bell.

When allowed, the command emits the terminal's `bel` capability, with control-G used as a fallback during terminal initialization. Whether this makes a sound, flashes the display, or does nothing depends on your terminal emulator and its bell settings. A successful command therefore does not guarantee an audible beep.

## Example

To enable EPIC's bell output and request a bell:

```text
/set beep on
/beep
```

## History

The `beep` command first appeared in ircII.

## Source

See [source/commands.c](../../source/commands.c) for the `beep` registration and `beepcmd()`, [source/term.c](../../source/term.c) for `term_beep()`, `term_emit()`, and the bell-capability fallback, and [source/screen.c](../../source/screen.c) for redraw suppression in `repaint_window_body()`. [source/vars.c](../../source/vars.c) registers the `beep` setting, and [include/config.h](../../include/config.h) defines its default.
