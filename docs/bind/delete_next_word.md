# delete_next_word

## Synopsis

```text
/bind <key> delete_next_word
```

## Description

Delete text forward from the input cursor to the start of the next word. Within a word, this removes the remainder of that word and any following separators. On a separator, it removes the run of separators up to the next word, leaving that word intact. If there is no next word, it removes everything from the cursor to the end of the line.

The deleted text replaces the cut buffer and can be restored with [yank_from_cutbuffer](yank_from_cutbuffer.md). The cursor returns to the start of the deleted region and the following text shifts left. At the end of the line, including on an empty line, nothing happens and the cut buffer is unchanged.

Words are separated by characters in [word_break](../set/word_break.md), which defaults to space and tab. If that setting is unset, space and tab are also used. Only ASCII characters below DEL are recognized as separators; non-ASCII characters added to the setting do not become separators for this action.

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes and does not send the input line or issue an IRC request.

The default binding is `^[d`: Escape followed by lowercase `d`. Scripts can change it.

## Example

```text
/bind ^[d delete_next_word
```

With the default word separators and `one two three` in the input line, invoking the action on the `t` in `two` leaves `one three` and saves `two ` in the cut buffer. Invoking it on the space immediately before `two` instead deletes only that space, leaving `onetwo three`.

See [bind](../commands/bind.md) for key sequence syntax and [forward_word](forward_word.md) for the movement used to select the deleted region.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Escape-d binding.
- [source/input.c](../../source/input.c): `input_delete_next_word`, `input_forward_word`, `WHITESPACE`, and `cut_input` select the region, save it, and redraw the input line.
- [source/ircaux.c](../../source/ircaux.c): `strext2` extracts the cut text and closes the gap.
- [include/config.h](../../include/config.h) and [source/vars.c](../../source/vars.c): `word_break` default and setting registration.
