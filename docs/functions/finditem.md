# finditem function

## Synopsis

`$finditem(<array> <string>)`

## Description

[$setitem()](setitem.md) stores text under an array name and an item number. `$finditem()` searches for that exact text and returns its item number, which can be passed to [$getitem()](getitem.md). Matching is case-sensitive and compares the complete string, rather than a prefix or wildcard pattern.

The array name is the first ordinary, unquoted word and is case-insensitive. The remaining text is the search string, including spaces and any literal quotation marks. Do not add double quotes to group a search string containing spaces: those quotes would become part of the value being searched for. An omitted search string searches for an empty value in an existing array.

The function performs a binary search through the array's sorted index. If multiple items contain the same string, it returns one of them; do not depend on which duplicate it selects. [$ifinditem()](ifinditem.md) returns the sorted index instead of the item number. To select the first duplicate in sorted order, use [$ifindfirst()](ifindfirst.md) and convert its result with [$indextoitem()](indextoitem.md). [$finditems()](finditems.md) is a prefix-search operation, rather than a list of all exact matches.

## Practical

Use this function to determine whether a particular value is present in a named array. Its case-sensitive exact comparison is more precise than general wildcard matching.

Searching can rebuild a deferred sorted index, but does not change item values or their item numbers. These named arrays are separate from `/assign` variable structures.

## Returns

| Result | Meaning |
| --- | --- |
| `-2` | No item contains the exact search string. |
| `-1` | The array does not exist, or no array name was supplied. |
| Nonnegative integer | The matching item's number. `0` is a valid match. |

## Examples

Given this array:

```irc
@ setitem(booya 0 blah)
@ setitem(booya 1 foobar)
@ setitem(booya 2 blah)
```

| Expression | Result |
| --- | --- |
| `$finditem(booya blah)` | `0` or `2`; either matching item may be selected. |
| `$finditem(booya foobar)` | `1`. |
| `$finditem(booya Blah)` | `-2`, because matching is case-sensitive. |
| `$finditem(missing_array blah)` | `-1` if that array does not exist. |

## Source

Registered in [source/functions.c](../../source/functions.c). The `FINDI` wrapper, exact binary search, array lookup, and sorted-index rebuilding are in [source/array.c](../../source/array.c). Ordinary-word parsing is defined in [include/words.h](../../include/words.h).
