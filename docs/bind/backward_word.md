# backward_word

## Synopsis

```text
/bind <key> backward_word
```

## Description

Move the cursor left to the beginning of the current or previous word in the input line. Within a word, the cursor moves to its first character. At the first character of a word, on a separator, or at the end of the input line, it moves backward through separators and the preceding word. At the start of the input line, it does not move.

Words are separated by characters in [word_break](../set/set_word_break.md), which defaults to space and tab. If that setting is unset, the action also uses space and tab. Only ASCII characters below DEL are recognized as separators; adding a non-ASCII character to `word_break` does not make it a separator for this action.

If movement reaches the start of the input line, the cursor stops there, even if the first character is a separator. For example, with two spaces followed by `hello`, invoking this action from the end of `hello` stops on the first space.

Moving into an earlier displayed section of a long input line can shift the visible input text to the left and redraw it. The built-in action does not modify or send the text. It takes no arguments or options; extra text supplied with the binding is ignored.

The default binding is `^[b`: Escape followed by lowercase `b`. A terminal that sends Escape followed by `b` for Alt-B can invoke the same binding. Scripts can change this assignment.

## Example

```text
/bind ^[b backward_word
```

With the default word separators and `one two` in the input line, invoking the action at the end of the line moves onto the `t` in `two`. Invoking it again moves onto the `o` in `one`.

See [bind](../commands/bind.md) for key sequence syntax and [forward_word](bind_forward_word.md) to move forward by a word.

## Source references

- [source/keys.c](../../source/keys.c): action registration and the default Escape-b binding.
- [source/input.c](../../source/input.c): `input_backward_word`, `WHITESPACE`, `input_move_cursor`, and `redraw_input` implement word boundaries, movement, and horizontal scrolling.
- [include/config.h](../../include/config.h) and [source/vars.c](../../source/vars.c): `word_break` default and setting registration.
