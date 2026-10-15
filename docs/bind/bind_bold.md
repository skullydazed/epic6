# bold

## Synopsis

```text
/bind <key> bold
```

## Description

Insert the boldface toggle character (Control-B, byte `0x02`) into the input line at the cursor. It appears as a reverse capital B in the input line, regardless of which key invokes the action. When EPIC displays text containing this character, it toggles boldface for subsequent text.

This built-in action respects [insert_mode](../set/set_insert_mode.md): it inserts the character in insert mode or replaces the character under the cursor in overwrite mode, then advances the cursor. It takes no arguments or options; extra text supplied with the binding is ignored. It edits the input line without sending it or issuing an IRC request.

The default binding is Control-B (`^B`). Scripts can change it.

## Other notes

The visible result depends on your terminal emulator and its terminal capabilities. With an ANSI foreground color from 0 through 7, EPIC also selects the corresponding bright foreground while boldface is active.

[mangle_display](../set/set_mangle_display.md) can strip bold attributes from displayed text when its `bold` option is enabled. There is no `bold_video` setting in EPIC6.

## Example

```text
/bind ^B bold
```

Press Control-B, type the text to mark, then press Control-B again to end boldface. The two toggle characters appear as reverse B characters while you edit the line; their displayed effect after sending depends on the receiving client's formatting support.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-B binding.
- [include/irc.h](../../include/irc.h): `BOLD_TOG` definition.
- [source/input.c](../../source/input.c): `insert_bold` and `input_add_character`.
- [source/screen.c](../../source/screen.c): bold toggle processing, stripping, and terminal attribute output.
- [source/term.c](../../source/term.c): reverse control-character display and `term_sgr`.
