# end_of_line

## Synopsis

```text
/bind <key> end_of_line
```

## Description

Set the input cursor to the last position of the logical input line, immediately after its final character. If necessary, the input line scrolls to the right until the cursor is visible.

This built-in action leaves the text unchanged and redraws the input line. On an empty line, or when the cursor is already at the end, the cursor stays there. It takes no arguments or options; extra text supplied with the binding is ignored. It does not send the input line or issue an IRC request.

The default binding is Control-E (`^E`). Scripts can change it.

## Example

```text
/bind ^E end_of_line
```

With `hello` in the input line and the cursor anywhere in that text, pressing Control-E places the cursor after the `o`, ready to append text.

See [bind](../commands/bind.md) for key sequence syntax and [beginning_of_line](beginning_of_line.md) to move to the start of the input line.

## Source references

- [source/keys.c](../../source/keys.c): action registration and the default Control-E binding.
- [source/input.c](../../source/input.c): `input_end_of_line` repeatedly calls `input_move_cursor` until it reaches the end, then calls `redraw_input` to update the display and horizontal scrolling.
