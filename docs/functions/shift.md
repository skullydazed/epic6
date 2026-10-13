# $shift()

## Synopsis

`$shift(<variable name>)`

## Description

Removes the first word from the named variable's value, writes the remaining text back, and returns the removed word. Supply the variable name rather than its expanded value. Only the first function argument is used; additional arguments are ignored. There is no literal-list form: `$shift(one two three)` operates on the variable `one`, not the list `one two three`.

Word parsing uses whitespace, with double-quoted grouping when [/xdebug dword](../commands/xdebug.md) is enabled. The returned word is dequoted in that mode. Leading whitespace before the first word and the separators before the remaining words are consumed. The remaining text is kept as a substring rather than rebuilt as a normalized list, so its internal spacing and quotes remain.

Variable names are case-insensitive and use normal lookup and assignment rules. Writeback updates an existing visible local variable; otherwise it is global. Explicit `:name` and `::name` select local or global writeback. See [/local](../commands/local.md) and [/assign](../commands/assign.md).

With only one word, the remaining value becomes empty. Empty global writeback removes the assigned value, while a local variable remains defined with an empty value. Unlike [$pop()](pop.md), the function still attempts writeback for an empty or undefined value. An invalid variable name can produce an assignment diagnostic.

## Practical

Use this destructive operation to consume a variable's word list while building FIFO or LIFO queues. [$push()](push.md) adds at the back, [$unshift()](unshift.md) adds at the front, and `$pop()` removes from the back. [$word()](word.md) retrieves a word without changing a variable. The [/shift command](../commands/shift.md) is a separate interface and can also operate on positional arguments.

## Returns

The removed first word. Missing input, an empty or undefined value, or an empty quoted first word returns empty. The function changes the named variable and needs no IRC connection. There are no options.

## Examples

After `assign foo blah booya`, `$shift(foo)` returns `blah` and leaves `foo` containing `booya`.

With `/xdebug dword` enabled, a value of `"one two" three` yields `one two` and leaves `three`.

Registration and `function_shift()`: [source/functions.c](../../source/functions.c). First-word parsing: [include/words.h](../../include/words.h) and [source/ircaux.c](../../source/ircaux.c). Variable lookup and writeback: [source/alias.c](../../source/alias.c).
