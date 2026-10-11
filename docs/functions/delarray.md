# $delarray()

## Synopsis

`$delarray(<array>)`

## Technical

Removes every item in a named array created with [`$setitem()`](setitem.md), and removes the array itself from EPIC's array registry. This releases its stored strings and indexing data. Other named arrays retain their contents.

The first whitespace-separated argument is the array name; additional arguments are ignored. Array names are case-insensitive because lookup uppercases the name. Double quotes do not group the name, even with `/xdebug dword` enabled. These named arrays are separate from ordinary [`/assign`](../commands/assign.md) variables and variable structures; deleting one does not delete similarly named variables.

## Practical

Use this function to clean up a named array when a script no longer needs it. To remove individual items while retaining the rest, use [`$delitem()`](delitem.md). After deletion, `$setitem()` can create a new array with the same name by setting item zero.

## Returns

| Value | Meaning |
| --- | --- |
| `0` | The array existed and was deleted. |
| `-1` | The array did not exist, or no name was supplied. |

No diagnostic is emitted for a missing array. A second deletion of the same array returns `-1` unless it has been recreated.

## Examples

```irc
@ result = setitem(temporary_names 0 Alice)
@ result = delarray(temporary_names)
```

The deletion returns `0`. A subsequent `$delarray(temporary_names)` returns `-1`.

## Source

Registered in [source/functions.c](../../source/functions.c). Parsing, array-name lookup, and removal are implemented in [source/array.c](../../source/array.c).
