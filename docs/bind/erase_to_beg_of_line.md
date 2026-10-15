# erase_to_beg_of_line

## Synopsis

```text
/bind <key> erase_to_beg_of_line
```

## Description

Erase all text before the cursor back to the beginning of the input line, without erasing the character under the cursor. The remaining text shifts to the beginning of the line, and the cursor moves there, after the input prompt.

The erased text replaces the cut buffer and can be restored with [yank_from_cutbuffer](yank_from_cutbuffer.md). At the end of a nonempty line, the action erases the whole line. At the beginning of the line, including on an empty line, no text is erased and the cut buffer is unchanged.

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes, redraws the input line, and does not send the line or issue an IRC request. It has no default key assignment.

## Example

```text
/bind ^X^U erase_to_beg_of_line
```

With `one two` in the input line and the cursor on the `t`, pressing Control-X followed by Control-U leaves `two`, moves the cursor onto its `t`, and saves `one ` in the cut buffer.

See [bind](../commands/bind.md) for key sequence syntax, [erase_to_end_of_line](erase_to_end_of_line.md) to erase forward, and [erase_line](erase_line.md) to clear the entire input line regardless of cursor position.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/input.c](../../source/input.c): `input_clear_to_bol` calls `cut_input(0, LOGICAL_CURSOR - 1)`, then resets the cursor and redraws the input line; `cut_input` uses logical character boundaries.
- [source/ircaux.c](../../source/ircaux.c): `strext2` saves the erased region in the cut buffer and shifts the remaining text left.
