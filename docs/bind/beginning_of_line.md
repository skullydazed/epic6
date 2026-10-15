# beginning_of_line

## Synopsis

```text
/bind <key> beginning_of_line
```

## Description

Set the input cursor position to the start of the logical input line. If necessary, the input line scrolls to the left until its beginning and the cursor are visible. The cursor rests after the input prompt, before the first character of editable text.

This built-in action leaves the text unchanged and redraws the input line. On an empty line, or when the cursor is already at the beginning, the cursor remains at the beginning. The action takes no arguments or options; extra text supplied with the binding is ignored. It does not send the input line or issue an IRC request.

The default binding is Control-A (`^A`). Scripts can change it.

## Example

```text
/bind ^A beginning_of_line
```

With `hello` in the input line, pressing Control-A places the cursor before the `h`, ready to insert or overwrite text according to the current input mode.

See [bind](../commands/bind.md) for key sequence syntax and [end_of_line](bind_end_of_line.md) to move to the end of the input line.

## Source references

- [source/keys.c](../../source/keys.c): action registration and the default Control-A binding.
- [source/input.c](../../source/input.c): `input_beginning_of_line` resets the logical cursor and displayed start position, then calls `redraw_input`.
