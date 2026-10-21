# Pop

Do you mean:

- The [pop command](../commands/pop.md), `/pop variable-name`, which removes the variable's final word and discards it?
- The [pop function](../functions/pop.md), `$pop(variable-name)`, which performs that mutation and returns the removed word?

Supply a variable name, not its expanded value. Both interfaces modify local client state and need no IRC connection. Word grouping depends on `xdebug dword`; an empty function result can indicate missing input or an empty value as well as removal of an empty quoted word. See the individual pages for scope and writeback details.

For removing the first word instead, see [shift](../functions/shift.md). For restoring stacked definitions or selecting a stacked window, see [stack](../commands/stack.md) or [window pop](../window/pop.md); these are separate operations.

## Source references

- `source/commands.c`: `pop` command wrapper discards the function result.
- `source/functions.c`: built-in registration and `function_pop`, which extracts the last word and writes the remainder.
