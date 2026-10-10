# shift

## Synopsis

```text
/shift <variable-name>
```

## Description

`shift` removes the first word from the named variable and assigns the remaining text back to it. The command calls the [shift function](../functions/shift.md) and discards its returned word, so it has no result value or normal output. Use the function when you want to retain the removed word.

Supply the variable's name, not its expanded value: `/shift items`, rather than `/shift $items`. Names are case-insensitive. Only the first variable-name argument is used; additional arguments are ignored. There are no options, count argument, or destination variable. With no argument, the command silently does nothing; it does not implicitly remove the first positional argument from `$*`.

Words normally end at whitespace. With `xdebug dword` enabled, a double-quoted group is treated as one word; otherwise quotation marks do not group words for this operation. Leading whitespace and the first word are removed, along with one separator character following that word. The remaining text is preserved rather than rebuilt as a normalized word list.

Writeback follows ordinary assignment rules: an existing visible local variable is updated; otherwise the assignment is global. `:name` and `::name` explicitly select local or global writeback. See [local](local.md) and [assign](assign.md). Invalid assignment names can produce the ordinary assignment diagnostic.

Removing the only word deletes a global assigned value, while a local variable remains defined with an empty value. An undefined variable with no other value to resolve does not acquire a nonempty value. Normal variable lookup applies, including its built-in and environment fallbacks; writeback creates or changes a script assignment, rather than changing a `set` setting or an environment variable.

## Examples

```text
assign items one two three
shift items
echo $items
```

This displays `two three`.

To keep the removed word as well:

```text
assign items one two three
@ first_word = shift(items)
echo $first_word
```

This displays `one`; `items` now contains `two three`.

## Related commands

[unshift](unshift.md) prepends text to a variable, while [pop](pop.md) removes its last word. The command is always registered and does not require an IRC connection.

## Source

Registration and `shift_cmd()` are in [source/commands.c](../../source/commands.c). `function_shift()` is in [source/functions.c](../../source/functions.c). Lookup and writeback are in [source/alias.c](../../source/alias.c). Function argument and word parsing are defined in [include/functions.h](../../include/functions.h) and [include/words.h](../../include/words.h), with implementations in [source/ircaux.c](../../source/ircaux.c) and [source/words.c](../../source/words.c).
