# parse_command

## Synopsis

```text
/bind <key> parse_command <commands>
/bind <key> parse_command { <command>; <command>; ... }
```

## Description

Execute the specified command or commands when the bound key or key sequence is pressed. Surround multiple commands with curly braces and separate them with semicolons, so the entire block belongs to the binding.

This built-in action runs its stored text through EPIC's script command parser with an empty argument string. Positional argument expandos such as `$*`, `$2`, and `$n-m` therefore expand as though no arguments were supplied. The current input line and pressed key are not passed as command arguments. Ordinary variable and function expansion follows the normal script parsing rules.

Commands are interpreted as script commands, so a leading `/` is optional. Their output, effects, and errors depend on the commands themselves. The binding action does not automatically submit or clear the input line; commands that edit input or send IRC requests can do so when explicitly included in the block. Server responses to any such requests depend on the server.

There are no action-specific options or default key assignments. Binding `parse_command` without command text is accepted, but invoking it does nothing.

## Examples

Print a message when Escape followed by lowercase `e` is pressed:

```text
/bind ^[e parse_command echo Key pressed
```

Run two commands when Control-X followed by Control-E is pressed:

```text
/bind ^X^E parse_command { echo First message; echo Second message; }
```

These examples produce local output and leave the input line intact.

See [bind](../commands/bind.md) for key sequence syntax and [echo](../commands/echo.md) for the example command.

## Source references

- [source/keys.c](../../source/keys.c): action registration, `bindcmd` command-text storage, and `key_exec` invocation.
- [source/input.c](../../source/input.c): `parse_text` passes stored commands to `runcmds` with an empty argument string and preserves the previous system-exception state.
- [source/commands.c](../../source/commands.c): `runcmds`, `parse_block`, and `parse_statement` process command blocks, semicolons, script syntax, and expansion.
