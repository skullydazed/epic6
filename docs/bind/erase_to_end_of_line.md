# erase_to_end_of_line

## Synopsis

```text
/bind <key> erase_to_end_of_line
```

## Description

Erase text from and including the input cursor to the end of the input line, subject to the long-line limitation below. Text before the cursor remains, and the cursor stays at the start of the erased region.

The erased text replaces the cut buffer and can be restored with [yank_from_cutbuffer](yank_from_cutbuffer.md). At the end of the line, including on an empty line, no text is removed. Invoking this action there can replace the cut buffer with an empty string, so do not use it to preserve a previous cut.

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes, redraws the input line, and does not send the line or issue an IRC request.

The default binding is Control-K (`^K`). Scripts can change it.

## Limitation

The implementation uses a fixed ending index of 9,999 and a byte-offset sentinel of the same value rather than an unlimited end-of-line boundary. On very long input lines, it can stop before the actual end or remove nothing when the cursor is beyond that boundary. Do not rely on complete erasure for lines exceeding 9,999 bytes or 10,000 logical input characters.

## Example

```text
/bind ^K erase_to_end_of_line
```

With `one two` in the input line and the cursor on the `t`, pressing Control-K leaves `one `, keeps the cursor after that space, and saves `two` in the cut buffer.

See [bind](../commands/bind.md) for key sequence syntax, [erase_to_beg_of_line](erase_to_beg_of_line.md) to erase backward, and [erase_line](erase_line.md) to clear the entire input line.

## Source references

- [source/keys.c](../../source/keys.c): action registration and the default Control-K binding.
- [source/input.c](../../source/input.c): `input_clear_to_eol` calls `cut_input(LOGICAL_CURSOR, 9999)`; `cut_input` and `retokenize_input` determine the ending byte offset and update the input line.
- [source/ircaux.c](../../source/ircaux.c): `strext2` saves the selected region in the cut buffer and removes it.
