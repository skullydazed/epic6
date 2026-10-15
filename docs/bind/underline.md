# underline

## Synopsis

```text
/bind <key> underline
```

## Description

Insert the underline toggle character (Control-underscore, byte `0x1f`) into the input line at the cursor. It appears as a reverse underscore in the input line, regardless of which key invokes the action. When EPIC displays text containing this character, it toggles underlining for subsequent text.

This built-in action respects [insert_mode](../set/insert_mode.md): it inserts the character in insert mode or replaces the character under the cursor in overwrite mode, then advances the cursor. It takes no arguments or options; extra text supplied with the binding is ignored. It edits the input line without sending it or issuing an IRC request.

The default binding is Control-underscore (`^_`). Scripts can change it; the physical key combination that sends this character depends on your terminal configuration.

## Other notes

The visible result depends on your terminal emulator and its terminal capabilities. [mangle_display](../set/mangle_display.md) can strip underline attributes from displayed text when its `underline` option is enabled.

A second underline toggle turns the attribute off again. [highlight_off](highlight_off.md) resets underlining together with the other formatting attributes.

## Example

```text
/bind ^_ underline
```

Invoke the bound key, type the text to underline, then invoke it again to end underlining. The two toggle characters appear as reverse underscores while you edit the line; their displayed effect after sending depends on the receiving client's formatting support.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-underscore binding.
- [include/irc.h](../../include/irc.h): `UND_TOG` definition.
- [source/input.c](../../source/input.c): `insert_underline` and `input_add_character`.
- [source/screen.c](../../source/screen.c): underline toggle processing, stripping, and terminal attribute output.
- [source/term.c](../../source/term.c): reverse control-character display and `term_sgr`.
