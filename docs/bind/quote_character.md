# quote_character

## Synopsis

```text
/bind <key> quote_character
```

## Description

Treat the next character you type as though it were bound to [self_insert](self_insert.md), rather than whatever action is actually assigned to it. This built-in action sets a flag on the input screen; after the next character passes through normal key handling, the flag is cleared.

The quoted character is inserted at the input cursor, respecting [insert_mode](../set/insert_mode.md). Control characters appear as reverse letters in the input line. Quoting bypasses keybinding lookup for that character; it does not escape the character from later command parsing or text formatting when the line is submitted.

The action takes no arguments or options; extra text supplied with the binding is ignored. It does not itself insert text or send an IRC request. The default binding is Control-Q (`^Q`), which scripts can change.

## Limitations

Quoting applies to one input character, not an entire terminal escape sequence. For a key such as an arrow key that sends several characters, only the first character is quoted; the rest are processed normally.

A prompt waiting specifically for a keypress receives the next character before normal keybinding handling, so that keypress does not consume the pending quote flag. The interactive keybinding input path requires fullscreen mode. EPIC can only quote characters that the terminal actually delivers to it.

## Example

```text
/bind ^Q quote_character
```

With default bindings, press Control-Q followed by Control-D to insert a literal Control-D character instead of invoking [delete_character](delete_character.md). It appears as a reverse capital D in the input line. Pressing Control-D again invokes its usual binding.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default Control-Q binding, and the quote override in `handle_keypress`.
- [source/input.c](../../source/input.c): `quote_char` sets the screen flag; `input_add_character` performs insertion.
- [source/screen.c](../../source/screen.c): `user_input_codepoint` handles keypress prompts and clears the quote flag after normal input processing.
- [source/term.c](../../source/term.c): reverse control-character display in the input line.
