# yank_from_cutbuffer

## Synopsis

```text
/bind <key> yank_from_cutbuffer
```

## Description

Insert the saved cut-buffer text into the input line at the cursor, shifting the following text right. The cut buffer is preserved, so repeated invocations insert the same text again. If the cut buffer has never been set, the action does nothing; an empty cut buffer adds no text.

The cut buffer contains text saved by input editing actions such as [delete_next_word](delete_next_word.md), [delete_previous_word](delete_previous_word.md), [delete_to_previous_space](delete_to_previous_space.md), [erase_line](erase_line.md), [erase_to_beg_of_line](erase_to_beg_of_line.md), and [erase_to_end_of_line](erase_to_end_of_line.md). It is shared across the client rather than maintained separately for each window. Ordinary [backspace](backspace.md) and [delete_character](delete_character.md) do not save text there. This is EPIC's own cut buffer, not the operating system clipboard.

This built-in action always inserts, regardless of [insert_mode](../set/insert_mode.md). It takes no arguments or options; extra text supplied with the binding is ignored. It redraws the input line without submitting it or sending an IRC request. The default binding is Control-Y (`^Y`), which scripts can change.

## Limitations

The handler does not enlarge the input buffer. If the combined text exceeds its current byte capacity, insertion can truncate the saved text or the original text after the cursor, potentially splitting a multibyte UTF-8 character. The saved cut buffer itself remains unchanged.

Cursor advancement counts the cut text's display columns but moves by logical characters. With ordinary single-column text it ends just after the insertion. Wide characters can make it move too far into the following text, so cursor placement is not reliable for all Unicode text.

## Example

```text
/bind ^Y yank_from_cutbuffer
```

With default bindings, type `hello`, press Control-U to erase the line and save it in the cut buffer, then press Control-Y to restore `hello`. Press Control-Y again to insert another copy, yielding `hellohello`, provided the input buffer has room.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Control-Y binding.
- [source/input.c](../../source/input.c): `input_yank_cut_buffer`, `ADD_TO_INPUT`, `cut_input`, and `input_move_cursor` implement insertion, saving, and cursor movement.
- [source/ircaux.c](../../source/ircaux.c): `strext2` stores cut text.
- [source/wcwidth.c](../../source/wcwidth.c): `input_column_count` counts display columns.
