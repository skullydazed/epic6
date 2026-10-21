# Unshift

Do you mean:

- The [unshift command](../commands/unshift.md), `/unshift variable-name text`, which prepends text to a variable?
- The [unshift function](../functions/unshift.md), `$unshift(variable-name data)`, which performs the same mutation and returns the updated value?

Supply the variable's name, not its expanded contents. The old nonempty value follows the new data with a separating space. With `xdebug dword` enabled, data containing literal spaces is quoted as one prepended item. See the individual pages for scope, quoting, empty input, and errors. These are client-side variable operations and do not send IRC requests.

Related word-list operations are [shift](shift.md), which removes from the front, [push](push.md), which adds at the back, and [pop](pop.md), which removes from the back.

## Source references

- `source/commands.c`: command wrapper discards the function result.
- `source/functions.c`: `function_unshift` lookup, concatenation, writeback, and registration.
