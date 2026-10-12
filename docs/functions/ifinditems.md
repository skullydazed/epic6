# ifinditems function

## Synopsis

`$ifinditems(<array> <string>)`

## Description

Searches the named array for a value beginning with `string` and returns one matching sorted index. Despite its plural name, it returns a single integer, not a list of all exact matches. Comparison is literal and case-sensitive; wildcard characters have no special meaning.

[$setitem()](setitem.md) assigns storage item numbers. Sorted indexes describe a separate view ordered by case-sensitive text and then storage item number. Use a successful result with [$igetitem()](igetitem.md), or convert it with [$indextoitem()](indextoitem.md). If several values share the prefix, any matching index may be selected. Deferred sorted indexes are rebuilt when necessary without changing stored text.

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the prefix, including spaces and literal quotation marks. Do not quote a prefix merely to group spaces. Parsing does not depend on `/xdebug dword`; additional words belong to the same prefix. An omitted prefix is empty and matches any stored value in a nonempty array.

For an exact string search, use [$ifinditem()](ifinditem.md); [$ifindfirst()](ifindfirst.md) returns the first exact duplicate. [$finditems()](finditems.md) returns a storage item number for a prefix match, with different failure handling.

## Practical

Use this function to check for a case-sensitive prefix in a named array. Test whether the returned value is nonnegative; index zero is valid. Do not iterate the result expecting every matching item.

## Returns

A nonnegative sorted index on success. When the prefix is absent from an existing array, the result is `-(insertion_position + 1)`, where the insertion position is the zero-based location the search would use in the sorted view. Recover that position as `-result - 1`; it may equal the array size when the prefix sorts after all values. The function does not insert anything.

An unknown array or missing array name returns `-1`. This overlaps with a failed search whose insertion position is zero; the result alone does not distinguish them. There is no diagnostic and no fixed `-2` no-match code.

## Examples

```irc
@ setitem(booya 0 blah)
@ setitem(booya 1 foobar)
@ setitem(booya 2 blah)
```

The sorted values are `blah`, `blah`, `foobar`.

| Expression | Result |
| --- | --- |
| `$ifinditems(booya bla)` | `0` or `1`; either retrieves `blah`. |
| `$ifinditems(booya foo)` | `2` |
| `$ifinditems(booya Blah)` | `-1` (insertion position zero). |
| `$ifinditems(booya cat)` | `-3` (insertion position two). |
| `$ifinditems(booya zebra)` | `-4` (insertion position three). |
| `$ifinditems(booya)` | A matching index in this nonempty array. |

## Source

Registered in [source/functions.c](../../source/functions.c). The `FINDI` wrapper, `find_items` prefix comparison, binary search, sorting, and insertion-position encoding: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
