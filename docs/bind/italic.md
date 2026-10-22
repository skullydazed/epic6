# italic

## Synopsis

```text
/bind <sequence> italic
```

## Description

Insert the italic toggle character (Control-P, byte `0x10`) into the input line at the cursor. When EPIC displays text containing this character, it toggles italics for subsequent text. The visible formatting depends on terminal support and display filtering.

The action respects [insert_mode](../set/insert_mode.md): it inserts the character in insert mode or replaces the character under the cursor in overwrite mode, then advances the cursor. It does not send the input line. Additional binding text is ignored. There is no default key assignment.

## Example

```text
/bind ^[i italic
```

Press Escape followed by lowercase `i`, type some text, then press the sequence again to insert the closing toggle. Formatting after sending also depends on the receiving client's support.

See [bind](../commands/bind.md) for sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): registration in `init_binds`.
- [include/irc.h](../../include/irc.h): `ITALIC_TOG`.
- [source/input.c](../../source/input.c): `insert_italic` and `input_add_character`.
- [source/screen.c](../../source/screen.c): italic toggle processing and display attributes.
