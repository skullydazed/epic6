# highlight_off

## Synopsis

```text
/bind <key> highlight_off
```

## Description

Insert the formatting reset character (Control-O, byte `0x0f`) into the input line at the cursor. When EPIC displays text containing this character, it turns off the highlighting in effect before it: bold, reverse, blink, underline, italic, alternate character set, and foreground/background colors, including colors established by recognized ANSI escape sequences.

The reset character appears as a reverse capital O in the input line, regardless of which key invokes the action. It resets formatting for subsequent displayed text; it does not remove formatting characters already present in the input line.

This built-in action respects [insert_mode](../set/insert_mode.md): it inserts the character in insert mode or replaces the character under the cursor in overwrite mode, then advances the cursor. It takes no arguments or options; extra text supplied with the binding is ignored. It edits the input line without sending it or issuing an IRC request.

The default binding is Control-O (`^O`). Scripts can change it.

## Other notes

[mangle_display](../set/mangle_display.md) can strip formatting resets when its `all_off` option is enabled. In that case the reset does not clear the current display attributes.

## Example

```text
/bind ^O highlight_off
```

With the default bindings, press Control-B to insert a [bold](bold.md) toggle, type the text to highlight, then press Control-O before typing ordinary text. The input line shows reverse B and O characters at those positions. After sending, the visible formatting depends on the receiving client's support.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-O binding.
- [include/irc.h](../../include/irc.h): `ALL_OFF` definition.
- [source/input.c](../../source/input.c): `highlight_off` and `input_add_character`.
- [source/screen.c](../../source/screen.c): `ALL_OFF` processing, `zero_attribute`, ANSI parsing, and `all_off` stripping.
- [source/term.c](../../source/term.c): reverse control-character display in the input line.
