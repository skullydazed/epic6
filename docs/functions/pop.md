# pop function

## Synopsis

`$pop(<variable name>)`

## Technical

Removes the last word from the named variable's value, writes the remaining text back, and returns the removed word. Supply the variable's name, not its expanded value. Only the first function word is used; additional arguments are ignored. There is no literal-list form: `$pop(one two three)` operates on the variable `one`, not the list `one two three`.

Variable names are case-insensitive and follow normal lookup and assignment rules. An existing visible local variable is updated; otherwise writeback is global. Explicit `:name` and `::name` select local or global writeback. See [/local](../commands/local.md) and [/assign](../commands/assign.md).

Word boundaries use whitespace, with double-quoted grouping when [/xdebug dword](../commands/xdebug.md) is enabled. The returned word is dequoted in that mode. Removing a word cuts the existing text rather than rebuilding a normalized word list; the separator immediately before the removed word is cut as well, while earlier spacing remains.

With only one word, the variable's remaining value becomes empty. This removes a global assigned value, while a local variable remains defined with an empty value. Empty or undefined values return the empty string without writeback. Missing input also returns the empty string. Invalid writeback names can produce an assignment diagnostic.

## Practical

This reverses the action of [$push()](push.md). For a FIFO, [$unshift()](unshift.md) can add words at the front and `pop` can remove them at the back. [$shift()](shift.md) removes from the front instead. The [/pop command](../commands/pop.md) discards the returned word.

## Returns

The removed final word. An empty return can mean missing input, an empty or undefined variable, or removal of an empty quoted word. The function modifies the named variable and requires no IRC connection.

## Examples

```text
assign booya one two three four
@ last_word = pop(booya)
echo $last_word
echo $booya
```

This displays `four`, then `one two three`.

With `/xdebug dword` enabled, a value of `one "two three"` yields `two three` and leaves `one`.

## History

First appeared in +7, after ircII and before EPIC.

## Source

Registration and `function_pop()`: [source/functions.c](../../source/functions.c). `last_arg()`: [source/ircaux.c](../../source/ircaux.c). Word parsing: [include/words.h](../../include/words.h). Variable lookup and writeback: [source/alias.c](../../source/alias.c).
