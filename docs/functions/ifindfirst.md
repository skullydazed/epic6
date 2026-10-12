# ifindfirst function

## Synopsis

`$ifindfirst(<array> <string>)`

## Description

Returns the smallest sorted index whose stored value is exactly `string`. Matching is literal and case-sensitive, not a wildcard or prefix search. If several items contain identical text, this selects the first of them in sorted order.

[$setitem()](setitem.md) assigns storage item numbers. Sorted indexes are a separate view of those items, ordered by case-sensitive text and then storage item number. Pass this function's successful result to [$igetitem()](igetitem.md), not directly to [$getitem()](getitem.md). The search rebuilds deferred sorted indexes if necessary without changing the stored values.

Unlike [$ifinditem()](ifinditem.md), which may select any identical match, this function walks backward from the binary-search match to find the first one. [$finditem()](finditem.md) instead returns a storage item number. [$ifinditems()](ifinditems.md) performs a prefix search, rather than listing every exact duplicate.

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the search string, including spaces and literal quotation marks. Do not add quotes merely to group spaces. Parsing does not depend on `/xdebug dword`; additional words belong to the same string.

## Practical

Use this function to test whether a particular nonempty string is present and to locate the first occurrence in the sorted view. Its case-sensitive comparison is more precise than general wildcard matching.

## Returns

| Result | Meaning |
| --- | --- |
| `0` or greater | First matching sorted index. Index zero is valid. |
| `-2` | Array exists, but the nonempty string is not found. |
| `-1` | Array does not exist, input is missing, or the search string is empty. |

An empty search string is not searched, even if the array stores an empty value. There is no diagnostic or insertion-index return.

## Examples

```irc
@ setitem(booya 0 blah)
@ setitem(booya 1 foobar)
@ setitem(booya 2 blah)
```

The sorted values are `blah`, `blah`, `foobar`.

| Expression | Result |
| --- | --- |
| `$ifindfirst(booya blah)` | `0` |
| `$igetitem(booya 0)` | `blah` |
| `$ifindfirst(booya foobar)` | `2` |
| `$ifindfirst(booya Blah)` | `-2` |
| `$ifindfirst(booya)` | `-1` |

## Source

Registered in [source/functions.c](../../source/functions.c). Exact binary search, backward duplicate scan, sorted-index maintenance, array lookup, and return codes: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
