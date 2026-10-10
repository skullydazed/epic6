# rbind

Show the key sequences bound to a particular binding action.

## Synopsis

```text
/rbind action
```

## Description

`rbind` searches the current keymap for sequences [bound](bind.md) to the named action. It is useful when you know the action you want, but do not know which keys invoke it. The search includes nested sequences, such as escape-prefixed keys, and all matching entries, including ordinary `self_insert` bindings.

The action name must exactly match a registered binding action, ignoring case. It is not a wildcard, abbreviation, key sequence, or arbitrary EPIC command name. Alias-backed actions registered through [bindctl](../functions/bindctl.md) can be inspected by their registered action names; the alias itself is not executed.

Only the first argument is used, with double-quoted argument handling; additional text is ignored. There are no options or default action. With no argument, the command silently does nothing. An unknown action prints `No such function name`. The special action `nothing` reports `You cannot list all unbound keys.` If a valid action has no bindings in the current map, no lines are displayed.

Each matching line shows the sequence in readable binding notation, its action, and any stored action arguments. It also includes a bracketed package label, or `[*]` for an empty label. See [bind](bind.md) for control-key and escape notation, and [package](package.md) for ownership labels.

This command only inspects bindings: it changes no key assignments and invokes no actions. It is always registered and needs no IRC connection. To run an action directly, use [parsekey](parsekey.md).

## Example

Find keys that move the cursor to the start of the input line:

```text
/rbind beginning_of_line
```

The result depends on the current keymap and any script changes to it.

## Source

Registration is in [source/commands.c](../../source/commands.c). `rbindcmd`, action lookup, recursive enumeration, and binding display are in [source/keys.c](../../source/keys.c), with case-insensitive list lookup in [source/list.c](../../source/list.c).
