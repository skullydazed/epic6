# forward_character

## Synopsis

```text
/bind <key> forward_character
```

## Description

Move the input cursor right by one character. At the end of the input line, including on an empty line, nothing happens. When movement crosses into a later displayed section of a long input line, the input line scrolls to reveal that section.

This built-in action moves by the input editor's logical character positions, rather than by bytes or terminal columns. For example, moving past an ordinary multibyte UTF-8 character takes one invocation, including when that character occupies two columns. The action does not change or send the input text, and takes no arguments or options; any extra text supplied with the binding is ignored.

The default bindings are `^[OC` and `^[[C`, the common escape sequences sent by the Right arrow key. Here `^[` means Escape. Scripts can change these bindings, and your terminal must send a matching sequence for the key to invoke the action.

## Example

```text
/bind ^X^F forward_character
```

Press Control-X followed by Control-F to move right once. With `hello` in the input line and the cursor on its `h`, the cursor moves onto the `e`; the text stays `hello`.

See [bind](../commands/bind.md) for key sequence syntax, [backward_character](backward_character.md) to move left, and [end_of_line](end_of_line.md) to move directly to the end.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Right arrow bindings.
- [source/input.c](../../source/input.c): `forward_character`, `input_move_cursor`, `retokenize_input`, and `redraw_input` implement movement, logical character boundaries, and horizontal scrolling.
