# igetrmatches function

## Synopsis

`$igetrmatches(<array> <string>)`

## Technical

Treats the stored array values as wildcard patterns and returns the sorted indexes of every pattern matching `string`. Each returned index identifies a pattern retrievable with [$igetitem()](igetitem.md). Matching is case-insensitive and tests the complete string using EPIC wildcards, not regular expressions.

This works like [$getrmatches()](getrmatches.md), except that results are sorted indexes rather than storage item numbers. It reverses [$igetmatches()](igetmatches.md), which uses one supplied pattern to test stored text. Every matching pattern is included; no best match is selected, and duplicate patterns have separate indexes.

Indexes are zero-based and returned in ascending order. The sorted view orders stored patterns case-sensitively, then by storage item number for equal values. Deferred sorted indexes are rebuilt when necessary without changing stored values or storage item numbers. Changing array contents can change indexes.

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the string to test, including spaces and literal quotation marks. Do not add quotes merely to group spaces. Parsing does not depend on `/xdebug dword`; extra words belong to the same string. An omitted string is empty and is still tested against the patterns.

## Practical

Use this function when an array contains patterns and you want all patterns matching one string, in sorted pattern order. Use `$igetmatches()` when the array instead contains text to be searched. These named arrays are created with [$setitem()](setitem.md), separately from `/assign` structures.

## Returns

A space-separated list of matching sorted indexes. No matches, an unknown array, or missing input returns empty without a diagnostic or negative error code.

## Examples

```irc
@ setitem(patterns 0 apple*)
@ setitem(patterns 1 *pie)
@ setitem(patterns 2 pear)
```

The sorted patterns are `*pie`, `apple*`, `pear`.

| Expression | Result |
| --- | --- |
| `$igetrmatches(patterns apple pie)` | `0 1` |
| `$igetrmatches(patterns PEAR)` | `2` |
| `$igetrmatches(patterns orange)` | Empty string |

```irc
fe ($igetrmatches(patterns apple pie)) sorted_index {
    echo $igetitem(patterns $sorted_index)
}
```

This prints only stored patterns matching `apple pie`.

## Source

Registered in [source/functions.c](../../source/functions.c). Reversed `GET_MATCHES` arguments, sorted traversal, and array lookup: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h). Wildcard matching: [source/reg.c](../../source/reg.c).
