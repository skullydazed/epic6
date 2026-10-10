# dump command

## Synopsis

```text
/dump
/dump all
/dump <alias|var|on> [<alias|var|on> ...]
```

## Description

`dump` mass-deletes user command aliases, global assigned variables, and/or `on` hooks from the running client. It can help clear script definitions before reloading them without restarting EPIC.

With no arguments, or when the argument text begins with `all`, it deletes all three categories. Despite the name, it does not save a dump to a file. It does not change `.ircrc` or other files; only current client state is affected.

| Category | What is deleted |
| --- | --- |
| `alias` | User command/function alias bodies, argument lists, stubs, and package metadata. |
| `var` | Global user variable values, stubs, and package metadata. Local variables in active scopes are unaffected. |
| `on` | Current event and numeric hooks. |
| `all` | All three categories above. |

Built-in commands, functions, expandos, and settings are preserved. Other resources such as timers, key bindings, arrays, windows, channels, and connections are not reset by this C command. Removing a script's aliases and hooks can therefore leave other resources behind; this is not a complete restart or universal script unload. See [`unload`](unload.md) for package-based removal.

## Arguments and output

Category names are case-insensitive. `alias`, `var`, and `on` can be abbreviated to a nonempty prefix, such as `a`, `v`, or `o`, and combined as separate arguments. The special all-categories check is different: it requires the first three characters of the argument text to be `all`, and ignores the rest. Thus `all extra` and even `alligator` select all categories, but `a` selects aliases only. `all` appearing after another category is not recognized as an additional category.

Unrecognized words produce `DUMP: Did not recognize ...`. Recognized categories still run. If none are recognized, the command prints usage and deletes nothing.

For each selected category, it reports `Dumping your global variables`, `Dumping your global aliases`, or `Dumping your ONs`, in that order. There is no confirmation prompt, undo operation, or return value. Reloading a script can recreate definitions from its saved source.

The command is available in every build and requires no server connection.

## Script interaction

The bundled [dump script](../../script/dump) defines an alias named `dump` with additional categories and different parsing. Those features belong to the script, not this built-in. An alias can override `/dump`; use `//dump` to select the C built-in explicitly. See [`alias`](alias.md).

## Examples

To delete only global user variables, or both aliases and hooks:

```text
//dump var
//dump alias on
```

To delete all three built-in categories:

```text
//dump all
```

These operations remove the selected definitions immediately.

## Source

See [source/alias.c](../../source/alias.c) for `dumpcmd()`, `destroy_var_aliases()`, and `destroy_cmd_aliases()`. [source/hook.c](../../source/hook.c) implements `flush_on_hooks()`; [source/commands.c](../../source/commands.c) registers the command and implements alias bypass with two slashes.
