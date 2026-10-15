# altcharset

## Synopsis

```text
/bind <key> altcharset
```

## Description

This built-in keybinding inserts the alternate character set toggle character (Control-E, byte `0x05`) at the cursor in the input line. The character appears as a reverse capital E. Insertion respects [insert_mode](../sets/insert_mode.md): in overwrite mode it replaces the character at the cursor instead. The cursor advances past the inserted character.

When EPIC displays text containing this character, it toggles the terminal's alternate character set attribute. Whether that changes the appearance of subsequent characters depends on the terminal emulator and its terminal capabilities. It does not convert text to Unicode or select a different text encoding; for ordinary Unicode/UTF-8 text, use the desired Unicode characters directly.

The action takes no arguments or options and has no default key assignment. It edits the input line without sending it or issuing an IRC request. Any extra text supplied with the binding is ignored.

## Example

```text
/bind ^X^A altcharset
```

Press Control-X followed by Control-A to insert the toggle. Insert another toggle after the text that should use the alternate character set to turn the attribute off again.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/input.c](../../source/input.c): `insert_altcharset` and `input_add_character`.
- [include/irc.h](../../include/irc.h): `ALT_TOG` character definition.
- [source/screen.c](../../source/screen.c): alternate character set toggle processing and terminal attributes.
- [source/term.c](../../source/term.c): reverse control-character display in the input line and terminal attribute output.
