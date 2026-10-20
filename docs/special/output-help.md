# My output is all screwed up!

This guide covers terminal-display problems and the related case where Alt-number shortcuts do not select windows. For missing messages rather than damaged rendering, also check [levels](levels.md), [window level](../window/level.md), and [hold mode](../window/hold_mode.md).

## About terminal emulators

A terminal emulator translates keypresses into input bytes and renders the output bytes produced by EPIC. Special keys and display attributes depend on conventions shared by the emulator, terminal description, and application. A multiplexer or remote session can add another layer between them.

EPIC reads the `TERM` environment variable during terminal initialization and loads the corresponding terminfo description. The name must describe the terminal interface EPIC actually sees, including a multiplexer's virtual terminal when applicable. A missing or unsupported description makes terminal initialization fall back to non-fullscreen operation. Changing `TERM` text alone does not make an emulator implement another terminal's capabilities.

For incorrect cursor placement, broken clearing, or unexpected escape text, check the terminal description and emulator configuration. For incorrect text characters or box graphics, check character encoding and font support as well; see [input handling](input_handling.md) and [graphics characters](graphics_chars.md). EPIC's `encoding console` setting describes incoming terminal bytes, so it must match what the terminal sends.

Formatting can also be changed by [mangle_display](../set/mangle_display.md), output-rewrite scripts, or [on window](../on/window.md) hooks. Check the relevant setting or script when only particular messages are affected. A redraw cannot repair an incorrect terminal description or encoding.

## Alt-number does not switch to a window

### The problem

Alt-number does not have a universal terminal representation. An emulator might send Escape followed by the digit, send a different character or sequence, send the unmodified digit, or handle the shortcut itself without delivering it to EPIC.

### The reason

EPIC matches input sequences, not a physical Alt modifier. A binding for Escape followed by `2` cannot match an unrelated character or a shortcut intercepted by the terminal. EPIC6 decodes console input to Unicode first, and code points above 127 are inserted directly into the input line rather than matched as custom bindings. An eight-bit “Meta” character is therefore not interchangeable with Escape followed by an ASCII digit.

Alt-number window selection also requires a binding supplied by your configuration; it is not guaranteed merely by using a terminal that sends Escape-digit. See [default keybindings](default_keybindings.md).

### The solution

Configure the terminal to send Escape followed by the digit for the shortcut you want, and check that the terminal or multiplexer is not consuming it. Then inspect or define the matching EPIC binding:

```irc
/bind ^[2
/bind ^[2 parse_command { window refnum_or_swap 2; }
```

Here `^[` means Escape. The example selects window refnum `2`, or attempts to swap it into view if hidden; that window must exist, and hidden-window swapping requires the participants to be swappable. Substitute the window and sequence you actually want. Binding changes do not send an IRC request, and a binding cannot recover a key sequence that never reaches the client.

If Escape is both a standalone action and a prefix of a longer sequence, [key_interval](../set/key_interval.md) controls the ambiguous-prefix timeout. Change it only after confirming that the sequence and action are correct. Scripts can replace bindings or suppress matched actions through [on keybinding](../on/keybinding.md).

## Source references

- `source/term.c`: `term_init()`, `TERM`, terminfo loading, and output capabilities.
- `source/screen.c`: console decoding, normalization, and display output.
- `source/keys.c`: sequence notation, direct insertion above ASCII, matching, and timeouts.
- `source/window.c`: window selection and `refnum_or_swap`.
- `source/vars.c`: display-mangling settings.
