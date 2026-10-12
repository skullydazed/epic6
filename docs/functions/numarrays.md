# $numarrays()

## Synopsis

`$numarrays()`

## Technical

Returns the number of named arrays currently in EPIC's array registry. These are arrays created with [$setitem()](setitem.md), separate from `/assign` structures or local variables. Arguments are ignored.

The count is of arrays, not their items or the total number ever created. Creating a new array increases it; adding or replacing items in an existing array does not. Deleting an array with [$delarray()](delarray.md), or deleting its final item, removes it from the registry and decreases the count. Names are case-insensitive, so differing capitalization does not create a second array with the same name.

## Practical

Use this to report how many arrays currently exist. [$getarrays()](getarrays.md) lists their names, and [$numitems()](numitems.md) counts the items in one array. The count does not identify an unused name: deleting an array can leave gaps in a numbered naming scheme.

## Returns

A nonnegative integer, with `0` when no arrays exist. The call does not create, delete, or modify arrays and has no error sentinel.

## Examples

If no arrays exist, `$numarrays()` returns `0`. Creating `sample` at item zero makes the count `1`; adding another item to `sample` leaves it `1`. Deleting `sample` makes it `0` again, provided no other arrays were created.

`$numarrays(ignored)` returns the same count as `$numarrays()`.

Registration: [source/functions.c](../../source/functions.c). `function_numarrays()`, registry creation, and deletion: [source/array.c](../../source/array.c).
