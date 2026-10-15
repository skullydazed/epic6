# transpose_characters (removed binding)

## Former synopsis

```text
/bind <key> transpose_characters
```

## Description

The former `transpose_characters` keybinding swapped the character under the input cursor with the character immediately before it. Its documented behavior left the input unchanged at the end of the line or when no character preceded the cursor. EPIC6 does not implement or register this binding action.

Attempting to bind a valid key sequence to `transpose_characters` in an unmodified EPIC6 session reports `No such function transpose_characters` and leaves the binding unchanged. There is no default key assignment or supported argument list for this removed action. A script could define an action with this name, but its behavior would belong to that script.

Use the supported input editing actions, such as [backward_character](../bind/backward_character.md), [delete_character](../bind/delete_character.md), and [self_insert](../bind/self_insert.md), to edit text manually. A custom transposition operation requires a script.

## Example

This obsolete binding produces the diagnostic described above:

```text
/bind ^T transpose_characters
```

See [bind](../commands/bind.md) for supported binding operations.

## Source references

- [source/keys.c](../../source/keys.c): `init_binds` lists available actions; `bind_compressed_string` rejects unknown action names before changing the binding.
- [source/input.c](../../source/input.c): current input editing implementation has no transpose handler.
- [include/input.h](../../include/input.h): a leftover declaration for `input_transpose_characters` does not provide an implementation or register a binding action.
