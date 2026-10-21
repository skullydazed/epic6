# Push

Do you mean:

- The [push command](../commands/push.md), `/push variable-name text`, which appends text to a variable?
- The [push function](../functions/push.md), `$push(variable-name data)`, which performs the same append and returns the updated value?

Supply the variable's name, not its expanded contents. A nonempty existing value receives a separating space before the appended data. With `xdebug dword` enabled, data containing literal spaces is quoted as one appended item; see the individual pages for quoting, scope, and empty-input behavior. These operations change local client variables and require no IRC connection.

For removing the final item, see [pop](pop.md). For adding at the beginning, see [unshift](unshift.md). To save command, variable, or setting definitions on a stack, see [stack](../commands/stack.md); to save a window selection, see [window push](../window/push.md). Those are different operations from appending to a word list.

## Source references

- `source/commands.c`: command wrapper discards `function_push`'s return value.
- `source/functions.c`: append implementation and function registration.
