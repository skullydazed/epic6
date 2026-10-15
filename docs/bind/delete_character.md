# delete_character

## Synopsis

```text
/bind <key> delete_character
```

## Description

Delete the character currently under the input cursor, then shift the following text left to close the gap. The cursor stays at the same logical position, now pointing to the next character or to the end of the line. At the end of the input line, including on an empty line, nothing happens.

This built-in action uses the input editor's logical character boundaries rather than deleting a single byte, so an ordinary multibyte UTF-8 character is removed as a unit. The amount of space closed on screen depends on the deleted character's display width.

The action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes. Deleted text is not saved in the cut buffer. It edits the input line without sending it or issuing an IRC request.

The default binding is Control-D (`^D`). EPIC also binds the terminal's `key_dc` sequence, when available, to this action; this normally corresponds to the Delete key. Scripts can change these bindings.

## Example

```text
/bind ^D delete_character
```

With `hello` in the input line and the cursor on the `e`, pressing Control-D leaves `hllo`, with the cursor on the first `l`.

See [bind](../commands/bind.md) for key sequence syntax and [backspace](backspace.md) to delete the character before the cursor.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default Control-D binding, and terminal `key_dc` binding.
- [source/input.c](../../source/input.c): `input_delete_character`, `retokenize_input`, and the cursor-position macros define deletion boundaries and redraw the input line.
- [source/ircaux.c](../../source/ircaux.c): `ov_strcpy2` shifts the remaining bytes left.
