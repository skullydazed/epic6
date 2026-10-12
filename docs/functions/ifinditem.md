# ifinditem function

## Synopsis

`$ifinditem(<array> <string>)`

## Description

Returns the sorted index of an item whose stored text is exactly `string`. Matching is literal and case-sensitive, not a wildcard or prefix search. Because this uses a binary search, duplicate values can yield any matching index; no first-duplicate guarantee is made. Use [$ifindfirst()](ifindfirst.md) for the first matching sorted index.

[$setitem()](setitem.md) assigns storage item numbers, while sorted indexes describe a separate ordering by case-sensitive text and then storage item number. A successful result can be passed to [$igetitem()](igetitem.md) to retrieve the matching text. [$finditem()](finditem.md) instead returns a storage item number. [$ifinditems()](ifinditems.md), despite its plural name, returns one prefix-matching index rather than a list of exact duplicates.

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the search string, including spaces and literal quotation marks. Do not add quotes merely to group spaces. Parsing does not depend on `/xdebug dword`; additional words belong to the same string. Omitting the string searches for an empty stored value, unlike `$ifindfirst()`.

The search rebuilds deferred sorted indexes if necessary without changing stored values.

## Practical

Use this function to check whether a particular string is present in a named array. Its case-sensitive comparison is more precise than general pattern matching. Test whether the result is nonnegative; index zero is valid.

## Returns

`0` or greater is a matching sorted index. `-2` means no exact match in an existing array. `-1` means the array does not exist or the array name is missing. There is no diagnostic or insertion-index return.

## Examples

```irc
@ setitem(booya 0 blah)
@ setitem(booya 1 foobar)
@ setitem(booya 2 blah)
```

The sorted values are `blah`, `blah`, `foobar`.

| Expression | Result |
| --- | --- |
| `$ifinditem(booya blah)` | `0` or `1`; either index retrieves `blah`. |
| `$ifindfirst(booya blah)` | `0` |
| `$ifinditem(booya foobar)` | `2` |
| `$ifinditem(booya Blah)` | `-2` |
| `$ifinditem(booya)` | `-2` for this array, which has no empty value. |

## Source

Registered in [source/functions.c](../../source/functions.c). The `FINDI` wrapper, exact binary search, sorting, array lookup, and return codes: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
