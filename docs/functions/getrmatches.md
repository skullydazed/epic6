# getrmatches function

## Synopsis

`$getrmatches(<array> <string>)`

## Technical

Treats the items in `array` as wildcard patterns and returns the item numbers of every pattern matching `string`. This reverses [$getmatches()](getmatches.md), which treats the stored items as text and tests them against a supplied pattern. Matching uses EPIC's case-insensitive wildcard matcher, not regular expressions, and tests the complete string.

The array name is the first ordinary, unquoted word and is case-insensitive. All remaining text is the string to test, including spaces and literal quotation marks. Do not add quotes merely to group spaces. This parsing does not depend on `/xdebug dword`; additional text is part of the same string.

Results are zero-based storage item numbers in ascending order, rather than item values or sorted indexes. Duplicate patterns in separate items produce separate numbers. Every matching pattern is included; no best match is selected. [$igetrmatches()](igetrmatches.md) instead traverses sorted values and returns sorted indexes. This function does not modify array contents or their sorted indexes.

## Practical

Use [$getmatches()](getmatches.md) to retrieve all items matching one supplied pattern. Use `$getrmatches()` when the array itself holds patterns and you want to find which of them match one supplied string. Fetch each returned item's pattern with [$getitem()](getitem.md).

## Returns

A space-separated list of matching item numbers, or the empty string when nothing matches or the array does not exist. Missing input also returns empty. There is no negative error code or diagnostic. An empty string to test is still tested against the stored patterns; it is not a request to list all items.

## Examples

Given this array:

```irc
@ setitem(patterns 0 apple*)
@ setitem(patterns 1 *pie)
@ setitem(patterns 2 pear)
@ setitem(patterns 3 APPLE*)
```

| Expression | Result |
| --- | --- |
| `$getrmatches(patterns apple pie)` | `0 1 3` |
| `$getrmatches(patterns PEAR)` | `2` |
| `$getrmatches(patterns orange)` | Empty string |

```irc
fe ($getrmatches(patterns apple pie)) item_number {
    echo $getitem(patterns $item_number)
}
```

This prints only the stored patterns that match `apple pie`.

## Source

Registered in [source/functions.c](../../source/functions.c). The reversed `GET_MATCHES` arguments, array lookup, and storage-order traversal: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h). Wildcard matching: [source/reg.c](../../source/reg.c).
