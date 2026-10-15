# reverse

## Synopsis

```text
/bind <key> reverse
```

## Description

Insert the reverse-video toggle character (Control-V, byte `0x16`) into the input line at the cursor. It appears as a reverse capital V in the input line, regardless of which key invokes the action. When EPIC displays text containing this character, it toggles reverse video for subsequent text, normally swapping the foreground and background colors.

This built-in action respects [insert_mode](../set/insert_mode.md): it inserts the character in insert mode or replaces the character under the cursor in overwrite mode, then advances the cursor. It takes no arguments or options; extra text supplied with the binding is ignored. It edits the input line without sending it or issuing an IRC request.

The default binding is Control-V (`^V`). Scripts can change it.

## Other notes

The visible result depends on your terminal emulator and its terminal capabilities. [mangle_display](../set/mangle_display.md) can strip reverse-video attributes from displayed text when its `reverse` option is enabled.

A second reverse toggle turns the attribute off again. [highlight_off](highlight_off.md) resets reverse video together with the other formatting attributes.

## Example

```text
/bind ^V reverse
```

Press Control-V, type the text to mark, then press Control-V again to end reverse video. The two toggle characters appear as reverse V characters while you edit the line; their displayed effect after sending depends on the receiving client's formatting support.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-V binding.
- [include/irc.h](../../include/irc.h): `REV_TOG` definition.
- [source/input.c](../../source/input.c): `insert_reverse` and `input_add_character`.
- [source/screen.c](../../source/screen.c): reverse toggle processing, stripping, and terminal attribute output.
- [source/term.c](../../source/term.c): reverse control-character display and `term_sgr`.
