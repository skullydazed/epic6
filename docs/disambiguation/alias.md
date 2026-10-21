# Alias

Do you mean:

- The [alias command](../commands/alias.md), which defines, lists, or removes user-created commands and function aliases?
- The [aliasctl function](../functions/aliasctl.md), which inspects and manipulates alias and variable symbol tables programmatically?

Use `/alias name { commands }` to define a command body. Use `$aliasctl(...)` when you need a function result for symbol-table queries or changes. Both operate locally in the client; defining an alias does not execute its body or send an IRC request.

## Source references

- `source/alias.c`: alias command handling and symbol-table operations.
- `source/functions.c`: `aliasctl` registration and function interface.
