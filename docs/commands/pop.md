# pop

Remove the final word from a variable's value.

## Synopsis

```text
/pop variable-name
```

## Technical

`pop` removes the last word of the named variable and writes the remaining text back to that variable. Supply its name, not its expanded value: `/pop booya`, rather than `/pop $booya`. The name is case-insensitive and follows normal variable lookup and assignment rules. An existing visible local variable is updated; otherwise the assignment is global. Explicit `:name` and `::name` forms select local or global writeback. See [local](local.md) and [assign](assign.md).

Word boundaries normally use whitespace. Double-quoted words are recognized when `xdebug dword` is enabled; without it, quotation marks do not group words for this operation. Removing the word also removes its preceding word separator; remaining text is not reconstructed into a newly normalized list.

If a global variable has only one word, popping it removes its assigned value. A local variable instead remains defined with an empty value. An empty or undefined value is left alone. With no argument the command silently does nothing. Only one variable-name argument is used; additional text is ignored. There are no options or destination-variable argument.

The command is a wrapper around the [pop function](../functions/pop.md). It discards the function's returned word, so it has no result value or normal output. To retain the removed word, use the function in an assignment. Invalid writeback names can produce the ordinary assignment diagnostic.

## Examples

```text
assign booya one two three four
pop booya
echo $booya
```

This displays `one two three`.

To capture the removed word as well as shorten the list:

```text
assign booya one two three four
@ last_word = pop(booya)
echo $last_word
```

This displays `four`; `booya` now contains `one two three`.

## Related commands and history

The [push function](../functions/push.md) appends text to a variable; the [shift function](../functions/shift.md) removes its first word. [stack](stack.md) provides a separate stack mechanism for saving and restoring definitions and settings.

The `pop` command first appeared in `+7`, after ircII and before EPIC. It is always registered and requires no IRC connection.

## Source

Registration and `pop_cmd` are in [source/commands.c](../../source/commands.c). `function_pop` is in [source/functions.c](../../source/functions.c), variable lookup/writeback in [source/alias.c](../../source/alias.c), and final-word extraction in [source/ircaux.c](../../source/ircaux.c), using word-mode definitions in [include/words.h](../../include/words.h).
