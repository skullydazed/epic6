# getmatches function

## Synopsis

`$getmatches(<array> <pattern>)`

## Technical

Treats each array item as literal text and returns the item numbers of all values matching `pattern`. Matching uses EPIC's case-insensitive wildcard matcher, not regular expressions. The pattern tests the complete item value, including any spaces; use surrounding `*` wildcards for a substring search.

This reverses [$getrmatches()](getrmatches.md), which treats the stored items as patterns and tests them against one supplied string. [$igetmatches()](igetmatches.md) instead traverses sorted values and returns their sorted indexes.

The array name is the first ordinary, unquoted word and is case-insensitive. All remaining text is the pattern, including spaces and literal quotation marks. Do not surround the pattern with quotes merely to group spaces. This parsing does not depend on `/xdebug dword`; additional text is part of the pattern, rather than another search.

The result contains zero-based storage item numbers in ascending order, not sorted indexes or the matching values themselves. Equal matching values in different items produce separate item numbers. No array contents or sorted indexes are changed; these are named arrays created by [$setitem()](setitem.md), separate from `/assign` structures.

## Practical

Use this function to retrieve every matching item's number, then use [$getitem()](getitem.md) to fetch its value. It selects all matches rather than choosing only a best match.

## Returns

A space-separated list of matching item numbers, or the empty string when no item matches or the array does not exist. Missing input also returns empty. No diagnostic or negative error code is returned.

## Examples

Given this array:

```irc
@ setitem(sample 0 zebra)
@ setitem(sample 1 apple pie)
@ setitem(sample 2 APPLE)
```

| Expression | Result |
| --- | --- |
| `$getmatches(sample apple*)` | `1 2` |
| `$getmatches(sample apple)` | `2` |
| `$getmatches(sample *)` | `0 1 2` |
| `$getmatches(sample *pear*)` | Empty string |

```irc
fe ($getmatches(sample apple*)) item_number {
    echo $getitem(sample $item_number)
}
```

## Source

Registered in [source/functions.c](../../source/functions.c). The `GET_MATCHES` wrapper, array lookup, and storage-order traversal: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h). Wildcard matching: [source/reg.c](../../source/reg.c).
