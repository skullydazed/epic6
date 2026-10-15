# type_text

## Synopsis

```text
/bind <key> type_text <text>
```

## Description

Insert the specified text literally into the input line at the cursor, bypassing the characters' keybindings. Existing text after the cursor is shifted right. This built-in action always inserts, regardless of [insert_mode](../set/insert_mode.md), and leaves the cut buffer unchanged.

The action inserts stored text directly; it does not invoke [xtype](../commands/xtype.md) or dispatch each character through [self_insert](self_insert.md). `xtype -l` is a related way to enter literal text, but it follows insert/overwrite mode and grows the input buffer as needed, unlike this action.

Everything after the action name is the text to insert; there are no options or default key assignments. The action itself does not expand variables or execute commands in that text. Normal parsing of the command that installs or invokes the binding can still perform expansion. Literal insertion does not prevent formatting characters or command text from having meaning when the input line is later submitted.

The action redraws the input line without submitting it or sending an IRC request.

## Limitations

Always supply nonempty text. The binding parser supplies a null text pointer when text is omitted, and this handler does not check it before passing it to string routines; invoking such a binding can crash the client.

The handler does not enlarge the input buffer. If the combined text exceeds its current byte capacity, insertion can truncate the new text or the original text after the cursor, potentially splitting a multibyte UTF-8 character.

Cursor advancement counts the inserted text's display columns but moves by logical characters. With ordinary single-column text it ends just after the insertion. Wide characters can make it move too far into the following text, so cursor placement is not reliable for all Unicode text.

## Example

```text
/bind ^Xh type_text hello
```

Press Control-X followed by lowercase `h` to insert `hello` at the cursor. For example, inserting it before `world` produces `helloworld` when the buffer has sufficient room.

See [bind](../commands/bind.md) for key sequence syntax and [reset_line](reset_line.md) to replace the whole input line.

## Source references

- [source/keys.c](../../source/keys.c): action registration and stored text handling in `bindcmd` and `bind_compressed_string`.
- [source/input.c](../../source/input.c): `type_text`, `ADD_TO_INPUT`, `input_move_cursor`, and `retokenize_input` implement direct insertion, fixed-capacity copying, and cursor movement.
- [source/wcwidth.c](../../source/wcwidth.c): `input_column_count` counts display columns.
- [source/commands.c](../../source/commands.c): `xtypecmd` implements the separate literal typing command.
