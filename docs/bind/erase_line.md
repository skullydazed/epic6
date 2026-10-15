# erase_line

## Synopsis

```text
/bind <key> erase_line
```

## Description

Clear the contents of the input line and move the cursor to its beginning, after the input prompt. The cleared text is not submitted or preserved in command history by this action.

For a nonempty line, this built-in action replaces the cut buffer with the cleared text, subject to the length limitation below. Use [yank_from_cutbuffer](yank_from_cutbuffer.md) to insert that saved text again. On an already empty line, the cut buffer is unchanged.

The action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes, redraws the input line, and sends nothing to an IRC server.

The default binding is Control-U (`^U`). Scripts can change it.

## Limitation

The current implementation saves only the first 1,000 logical input characters to the cut buffer when a line is longer than that. It still clears the entire input line, so any remaining text cannot be recovered from this cut buffer.

## Example

```text
/bind ^U erase_line
```

With `hello` in the input line, pressing Control-U empties the line and saves `hello` in the cut buffer.

See [bind](../commands/bind.md) for key sequence syntax and [reset_line](reset_line.md) for an action that replaces input without changing the cut buffer.

## Source references

- [source/keys.c](../../source/keys.c): action registration and the default Control-U binding.
- [source/input.c](../../source/input.c): `input_clear_line` calls `cut_input(0, 999)` for nonempty input, then clears the whole buffer and resets the cursor; `input_yank_cut_buffer` restores saved text.
- [source/ircaux.c](../../source/ircaux.c): `strext2` copies the selected text into the cut buffer.
