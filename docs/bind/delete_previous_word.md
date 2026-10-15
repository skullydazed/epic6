# delete_previous_word

## Synopsis

```text
/bind <key> delete_previous_word
```

## Description

Delete text backward from the input cursor to the beginning of the current or previous word. Within a word, the beginning of that word is removed. At the first character of a word, on a separator, or at the end of the line, the action moves backward through separators and the preceding word, deleting the region it crosses. The character under the original cursor is not deleted.

The deleted text replaces the cut buffer and can be restored with [yank_from_cutbuffer](yank_from_cutbuffer.md). The cursor moves to the beginning of the deleted region and the remaining text shifts left. At the start of the input line, including on an empty line, nothing happens and the cut buffer is unchanged.

Words are separated by characters in [word_break](../set/word_break.md), which defaults to space and tab. If that setting is unset, space and tab are also used. Only ASCII characters below DEL are recognized as separators; non-ASCII characters added to the setting do not become separators for this action. If backward movement reaches the start of the input line, deletion extends to that position even when the line begins with separators.

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes and does not send the input line or issue an IRC request.

The default bindings are `^[h` (Escape followed by lowercase `h`) and `^[^?` (Escape followed by DEL). Scripts can change them; the latter sequence may be sent by Alt-Backspace on some terminals.

## Example

```text
/bind ^[h delete_previous_word
```

With the default word separators and `one two three` in the input line, invoking the action on the `t` in `three` leaves `one three` and saves `two ` in the cut buffer. Invoking it instead on the `o` in `two` removes `tw`, leaving `one o three`.

See [bind](../commands/bind.md) for key sequence syntax and [backward_word](backward_word.md) for the movement used to select the deleted region.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default bindings.
- [source/input.c](../../source/input.c): `input_delete_previous_word`, `input_backward_word`, `WHITESPACE`, and `cut_input` select the region, save it, and redraw the input line.
- [source/ircaux.c](../../source/ircaux.c): `strext2` extracts the cut text and closes the gap.
- [include/config.h](../../include/config.h) and [source/vars.c](../../source/vars.c): `word_break` default and setting registration.
