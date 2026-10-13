# push function

## Synopsis

`$push(<variable name> <data>)`

## Description

Appends data to the named variable, inserting one space before it when the existing value is nonempty. An empty or undefined variable receives the data without an added leading separator. The function returns the updated value.

The entire data argument is considered one appended item. With [/xdebug dword](../commands/xdebug.md) enabled, data containing a literal space is surrounded by double quotes and embedded double quotes are escaped. Data without a literal space is not automatically quoted. With DWORD disabled, data is appended literally. This option does not split the supplied data into separate words or remove user-supplied quote characters.

The name is parsed as an assignment name rather than a quoted word. Supply its name, not its expanded contents or a leading `$`. Names are case-insensitive and follow normal lookup and assignment rules, including bracketed names. An existing visible local is updated; otherwise writeback is global. `:name` forces local writeback and `::name` forces global writeback. See [/local](../commands/local.md) and [/assign](../commands/assign.md).

Only the first whitespace separator after the name is consumed; additional leading or trailing whitespace in the received data remains part of the appended item. Ordinary EPIC function argument expansion occurs before the handler receives the text.

## Practical

Use this function to append items to a word list, for example when maintaining an ordered queue. [$pop()](pop.md) removes the last item, [$unshift()](unshift.md) adds at the front, and [$shift()](shift.md) removes from the front. Use consistent DWORD settings when adding and removing grouped items.

An undefined variable is created when nonempty data is supplied. The [/push command](../commands/push.md) performs the same append but discards the returned value.

## Returns

The new variable value. With a name but no data, returns the current value without writeback. Missing input or an invalid character directly after the parsed name returns the empty string without appending. Ordinary writeback validation can produce an assignment diagnostic. There are no options other than the word-mode setting and no IRC connection is required.

## Examples

After `assign blah one`, `$push(blah hello there)` returns `one "hello there"` with DWORD enabled, or `one hello there` with DWORD disabled. In either case, `blah` receives that returned value.

After `assign blah one`, `$push(blah two)` returns `one two` in either mode, and `$pop(blah)` subsequently returns `two` and leaves `one`.

## History

First appeared in +7, after ircII and before EPIC.

## Source

Registration and `function_push()`: [source/functions.c](../../source/functions.c). Name parsing and `malloc_strcat_word()`: [source/ircaux.c](../../source/ircaux.c). Variable lookup and assignment: [source/alias.c](../../source/alias.c).
