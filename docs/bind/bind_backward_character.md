# backward_character

## Synopsis

```text
/bind <key> backward_character
```

## Description

Move the cursor one character to the left in the input line. If the cursor is at the beginning of the input line, nothing happens. When moving left crosses into an earlier displayed section of a long input line, the input line scrolls to reveal that section.

This built-in action moves by the input editor's logical character positions, rather than by bytes or terminal columns. For example, moving past an ordinary multibyte UTF-8 character takes one invocation, including when that character occupies two columns. The action does not change or send the input text, and takes no arguments or options; any extra text supplied with the binding is ignored.

The default bindings are `^[OD` and `^[[D`, the common escape sequences sent by the Left arrow key. Here `^[` means Escape. Scripts can change these bindings, and your terminal must send a matching sequence for the key to invoke the action.

## Example

```text
/bind ^X^B backward_character
```

Press Control-X followed by Control-B to move left once. With `hello` in the input line and the cursor at its end, the cursor moves onto the `o`; the text stays `hello`.

See [bind](../commands/bind.md) for key sequence syntax, [forward_character](bind_forward_character.md) to move right, and [backspace](bind_backspace.md) to delete the preceding character.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default Left arrow bindings.
- [source/input.c](../../source/input.c): `backward_character`, `input_move_cursor`, `retokenize_input`, and `redraw_input` implement movement, logical character boundaries, and horizontal scrolling.
