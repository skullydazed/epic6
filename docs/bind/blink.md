# blink

## Synopsis

```text
/bind <key> blink
```

## Description

Insert the blink toggle character (Control-F, byte `0x06`) into the input line at the cursor. It appears as a reverse capital F in the input line, regardless of which key invokes the action. When EPIC displays text containing this character, it toggles the blink attribute for subsequent text.

This built-in action respects [insert_mode](../set/set_insert_mode.md): it inserts the character in insert mode or replaces the character under the cursor in overwrite mode, then advances the cursor. It takes no arguments or options; extra text supplied with the binding is ignored. It edits the input line without sending it or issuing an IRC request.

The default binding is Control-F (`^F`). Scripts can change it.

## Other notes

The visible result depends on your terminal emulator and its terminal capabilities. Some terminals do not blink text or use the attribute for another effect. With an ANSI background color from 0 through 7, EPIC also selects the corresponding bright background while blink is active.

[mangle_display](../set/set_mangle_display.md) can strip blink attributes from displayed text when its `blink` option is enabled. There is no `blink_video` setting in EPIC6.

## Example

```text
/bind ^F blink
```

Press Control-F, type the text to mark, then press Control-F again to end the blink attribute. The two toggle characters appear as reverse F characters while you edit the line; their displayed effect after sending depends on the receiving client's formatting support.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-F binding.
- [include/irc.h](../../include/irc.h): `BLINK_TOG` definition.
- [source/input.c](../../source/input.c): `insert_blink` and `input_add_character`.
- [source/screen.c](../../source/screen.c): blink toggle processing, stripping, and terminal attribute output.
- [source/term.c](../../source/term.c): reverse control-character display and `term_sgr`.
