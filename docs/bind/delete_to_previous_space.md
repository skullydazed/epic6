# delete_to_previous_space

## Synopsis

```text
/bind <key> delete_to_previous_space
```

## Description

Delete everything to the left of the input cursor until whitespace is found, without deleting that whitespace. If there is no preceding whitespace, deletion extends to the beginning of the input line. The character under the original cursor is not deleted.

If the character immediately before the cursor is whitespace, or the cursor is at the start of the input line, no text is deleted and the cut buffer is unchanged. Otherwise, the deleted text replaces the cut buffer and can be restored with [yank_from_cutbuffer](yank_from_cutbuffer.md). The cursor moves to the start of the deleted region and the remaining text shifts left.

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes and does not send the line or issue an IRC request. It has no default key assignment.

## Other notes

Whitespace is tested with the platform C library's `isspace` classification in the active locale, rather than [word_break](../set/word_break.md). This includes ordinary spaces and tabs. The implementation passes character code points directly to this byte-oriented classification function, so behavior for non-ASCII text is not reliably defined; do not rely on it to recognize Unicode whitespace.

Unlike [delete_previous_word](delete_previous_word.md), this action does not skip backward across whitespace to delete an earlier word.

## Example

```text
/bind ^X^D delete_to_previous_space
```

With `one two` in the input line and the cursor at its end, pressing Control-X followed by Control-D deletes `two`, leaving `one ` and saving `two` in the cut buffer. Invoking it again leaves the trailing space and cut buffer unchanged.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default key assignments.
- [source/input.c](../../source/input.c): `input_delete_to_previous_space`, `input_move_cursor`, and `cut_input` select the region, save it, and redraw the input line.
- [source/ircaux.c](../../source/ircaux.c): `strext2` extracts the cut text and closes the gap.
- [source/irc.c](../../source/irc.c): locale initialization.
