# quit_irc (removed binding)

## Former synopsis

```text
/bind <key> quit_irc
```

## Description

The former `quit_irc` keybinding asked whether you wanted to quit the client and exited after an affirmative answer. EPIC6 does not implement or register this binding action.

Attempting to bind a valid key sequence to `quit_irc` in an unmodified EPIC6 session reports `No such function quit_irc` and leaves the binding unchanged. There is no default key assignment or supported argument list for this removed action. A script could define an action with this name, but its behavior would belong to that script.

Use the [quit](../commands/quit.md) command to exit EPIC6. For a custom key-driven workflow, [parse_command](../bind/parse_command.md) runs script commands; reproducing the former confirmation prompt requires a script that explicitly implements that interaction.

## Example

This obsolete binding produces the diagnostic described above:

```text
/bind ^X^Q quit_irc
```

See [bind](../commands/bind.md) for supported binding operations.

## Source references

- [source/keys.c](../../source/keys.c): `init_binds` lists the available built-in actions; `bind_compressed_string` rejects unknown action names before changing the binding.
- [source/commands.c](../../source/commands.c): `quit` and `exit` are registered to `e_quit`, which invokes the client exit routine.
- [source/input.c](../../source/input.c): `parse_text` implements the `parse_command` binding action.
