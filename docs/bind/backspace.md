# backspace

## Synopsis

```text
/bind <key> backspace
```

## Description

Delete the character immediately before the cursor in the input line, moving the cursor left and closing the gap. If the input cursor is at the start of the input line, nothing happens. The input prompt cannot be erased this way.

This built-in action combines [backward_character](bind_backward_character.md) and [delete_character](bind_delete_character.md), with a check for the start of the line before either action runs. Deletion uses the input editor's logical character boundaries rather than deleting a single byte, so an ordinary multibyte UTF-8 character is removed as a unit.

The action takes no arguments or options; extra text supplied with the binding is ignored. It behaves the same in insert and overwrite modes. Deleted text is not saved in the cut buffer, and the action does not send the line or issue an IRC request.

By default, both Control-H (`^H`) and the DEL character (`^?`) are bound to `backspace`. Which sequence a physical Backspace key sends depends on your terminal configuration; scripts can also change these bindings.

## Example

```text
/bind ^H backspace
/bind ^? backspace
```

These bindings make either common Backspace sequence delete the preceding character. For example, with `hello` in the input line and the cursor at its end, pressing the bound key leaves `hell`.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default `^H` and `^?` bindings.
- [source/input.c](../../source/input.c): `input_backspace`, `backward_character`, `input_move_cursor`, `input_delete_character`, and `retokenize_input` implement cursor movement and deletion.
