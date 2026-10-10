# parsekey

Invoke a key-binding action directly, whether or not it is bound to a key.

## Synopsis

```text
/parsekey action [arguments]
```

## Description

`parsekey` provides direct access to the actions normally assigned to keys with [bind](bind.md). It is useful for rarely used actions that have no convenient key binding. The first argument must be the full, case-insensitive name of a registered binding action, such as `beginning_of_line`, `type_text`, or `parse_command`. It is an action name, rather than a key sequence or an arbitrary EPIC command name.

Remaining text is passed to the action as its argument string. The action decides whether and how to use it; many cursor and editing actions ignore it. `type_text` inserts supplied text into the current input line, while `parse_command` executes supplied EPIC command text. Alias-backed actions registered through [bindctl](../functions/bindctl.md) also work: the stored alias command receives the supplied text appended as arguments.

The shared dispatcher first raises the `keybinding` event with data `action-name 0 0`. A suppressing [on](on.md) hook prevents the action from running. The last zero represents a synthetic key value: `parsekey` does not simulate a physical key press, resolve a sequence in the keymap, or supply the character that originally triggered a binding. Actions that depend on an actual character, such as `self_insert`, therefore do not reproduce an ordinary typed character through this command.

With no action, the command silently does nothing. An unknown name reports `No such function name`. There are no options or default action, and the built-in is always registered. The command has no separate success output; effects and output depend on the action. Input and window actions require the relevant live client context. Actions can also run commands that require an IRC connection.

## Examples

Move the cursor to the beginning of the input line:

```text
/parsekey beginning_of_line
```

Insert text at the cursor:

```text
/parsekey type_text hello there
```

Execute an EPIC command through the binding action:

```text
/parsekey parse_command echo hello there
```

These examples invoke the actions without changing any key bindings.

## Source

Registration is in [source/commands.c](../../source/commands.c). `parsekeycmd`, action lookup, built-in action registration, alias-backed dispatch, and the `keybinding` hook are in [source/keys.c](../../source/keys.c). `type_text` and `parse_command` implementations are in [source/input.c](../../source/input.c).
