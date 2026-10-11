# finditems function

## Synopsis

`$finditems(<array> <string>)`

## Description

Searches the values stored by [$setitem()](setitem.md) for an item beginning with `string`. The current implementation returns **one item number**, rather than a list of all matching items. Its comparison is case-sensitive and matches the prefix literally; wildcard characters have no special meaning.

The array name is the first ordinary, unquoted word and is case-insensitive. All remaining text is the prefix, including spaces and literal quotation marks. Do not add double quotes to group a prefix containing spaces. An omitted prefix is empty and can match any item in an existing nonempty array.

The search uses the array's sorted index and can rebuild that index after deferred changes. It does not alter stored values or item numbers. If several items share the prefix, do not depend on which one is selected. [$finditem()](finditem.md) searches for one exact value; [$ifinditems()](ifinditems.md) uses the same prefix search but returns a sorted index instead of an item number. [$ifindfirst()](ifindfirst.md) finds the first sorted index for an exact match.

## Practical

Use this function to locate one item by a case-sensitive prefix when you already know that a match exists. It is not a reliable existence test for an arbitrary prefix; see the failure limitation below. These named arrays are separate from `/assign` variable structures.

## Returns and restrictions

A successful search returns a nonnegative item number, usable with [$getitem()](getitem.md). `0` is a valid item number. A missing array or missing array name returns `-1`.

The current no-match path does not return a consistent `-2` error. It takes the bitwise complement of the item number at the prefix's insertion position in sorted order. If that insertion position is beyond the last item, it reads outside the sorted-index array, so the result is undefined and EPIC may crash. Do not use a failed search result as an item number or rely on this function for safe no-match handling.

## Examples

Given this array:

```irc
@ setitem(booya 0 blah)
@ setitem(booya 1 foobar)
@ setitem(booya 2 blah)
```

| Expression | Result |
| --- | --- |
| `$finditems(booya bl)` | `0` or `2`, selecting one item beginning with `bl`. |
| `$finditems(booya foo)` | `1`. |
| `$finditems(booya foobar)` | `1`; a complete value also matches its prefix. |
| `$finditems(missing_array bl)` | `-1` if the array does not exist. |

## Source

Registered in [source/functions.c](../../source/functions.c). The `FINDI` wrapper, prefix binary search using `strncmp()`, no-match conversion, and sorted-index rebuilding are in [source/array.c](../../source/array.c). Ordinary-word parsing is defined in [include/words.h](../../include/words.h).
