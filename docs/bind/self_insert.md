# self_insert

## Synopsis

```text
/bind <character-or-key-sequence> self_insert
```

## Description

Insert the bound character into the input line at the cursor position. For a binding with multiple characters, insert only the final character of the sequence.

When [insert_mode](../set/insert_mode.md) is on, insert before the character under the cursor and shift the remaining text right. When it is off, replace the character under the cursor. At the end of the line, append the character in either mode. The cursor advances past the inserted character and the input display is updated.

This built-in action takes no arguments or options; extra text supplied with the binding is ignored. It inserts the character itself without invoking another binding for it. It does not submit the input line or issue an IRC request. Inserting a formatting or other control character does not prevent it from having its normal meaning when the line is later parsed or displayed.

## Defaults and limitations

Printable ASCII characters, including space, are bound to `self_insert` by default. Control-C (`^C`) and Control-G (`^G`) are also assigned to it, though the terminal must deliver those characters to EPIC for the bindings to run. Scripts can change these assignments.

Input characters with code points above 127 bypass keybinding lookup and directly use the same insertion routine. [quote_character](quote_character.md) also makes the next character use that routine regardless of its binding. Ordinary multibyte UTF-8 characters are encoded and inserted as characters, rather than as separate keybinding bytes.

## Examples

```text
/bind ^D self_insert
```

Control-D now inserts a literal Control-D into the input line, displayed as a reverse capital D, instead of deleting a character.

```text
/bind ^Xz self_insert
```

Control-X followed by lowercase `z` inserts only `z`.

See [bind](../commands/bind.md) for key sequence syntax and [type_text](type_text.md) to insert specified text rather than the triggering character.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default assignments, `key_exec` passing the final sequence character, and direct insertion in `handle_keypress`.
- [source/input.c](../../source/input.c): `input_add_character` implements UTF-8 encoding, insert/overwrite behavior, buffer expansion, and cursor movement.
- [source/term.c](../../source/term.c): control-character display in the input line.
