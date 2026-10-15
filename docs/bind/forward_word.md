# forward_word

## Synopsis

```text
/bind <key> forward_word
```

## Description

Move the input cursor forward to the beginning of the next word. Within a word, the action passes the rest of that word and any following separators. On a separator, it passes the run of separators to the next word. If there is no next word, it stops at the end of the input line. At the end of the line, including on an empty line, the cursor does not move.

Words are separated by characters in [word_break](../set/word_break.md), which defaults to space and tab. If that setting is unset, space and tab are also used. Only ASCII characters below DEL are recognized as separators; non-ASCII characters added to the setting do not become separators for this action.

Movement uses the input editor's logical character positions. Moving into a later displayed section of a long input line can shift the visible input text to the right and redraw it. This built-in action leaves the text unchanged and takes no arguments or options; extra text supplied with the binding is ignored. It does not send the line or issue an IRC request.

The default binding is `^[f`: Escape followed by lowercase `f`. A terminal that sends Escape followed by `f` for Alt-F can invoke the same binding. Scripts can change this assignment.

## Example

```text
/bind ^[f forward_word
```

With the default word separators and `one two three` in the input line, invoking the action on the `o` in `one` moves onto the `t` in `two`. Invoking it again moves onto the `t` in `three`, and once more moves to the end of the line.

See [bind](../commands/bind.md) for key sequence syntax and [backward_word](backward_word.md) to move backward by a word.

## Source references

- [source/keys.c](../../source/keys.c): action registration and the default Escape-f binding.
- [source/input.c](../../source/input.c): `input_forward_word`, `WHITESPACE`, `input_move_cursor`, and `redraw_input` implement word boundaries, movement, and horizontal scrolling.
- [include/config.h](../../include/config.h) and [source/vars.c](../../source/vars.c): `word_break` default and setting registration.
