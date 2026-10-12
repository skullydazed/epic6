# igetmatches function

## Synopsis

`$igetmatches(<array> <pattern>)`

## Technical

Works like [$getmatches()](getmatches.md), but returns sorted indexes rather than storage item numbers. The supplied pattern is tested against each complete stored value using EPIC's case-insensitive wildcard matcher, not regular expressions. Every match is returned, including separate indexes for duplicate values; no best match is selected.

The sorted view orders values case-sensitively, then by storage item number for equal values. Results are zero-based indexes in ascending order, suitable for [$igetitem()](igetitem.md). The function rebuilds deferred sorted indexes when needed without changing stored text or storage item numbers. Indexes can change when array contents change.

The array name is the first ordinary, unquoted word and is case-insensitive. The entire remaining text is the pattern, including spaces and literal quotation marks. Do not add quotes merely to group spaces. Parsing does not depend on `/xdebug dword`; additional words are part of the same pattern. An omitted pattern is empty and can match an empty stored value; it does not list all values.

[$igetrmatches()](igetrmatches.md) reverses the matching direction: stored values are patterns tested against one supplied string. These named arrays are created with [$setitem()](setitem.md), separately from `/assign` structures.

## Returns

A space-separated list of matching sorted indexes. No matches, an unknown array, or missing input returns empty without a diagnostic or negative error code.

## Examples

```irc
@ setitem(sample 0 zebra)
@ setitem(sample 1 apple pie)
@ setitem(sample 2 APPLE)
```

The sorted values are `APPLE`, `apple pie`, `zebra`.

| Expression | Result |
| --- | --- |
| `$igetmatches(sample apple*)` | `0 1` |
| `$igetmatches(sample apple)` | `0` |
| `$igetmatches(sample *)` | `0 1 2` |
| `$igetmatches(sample *pear*)` | Empty string |

```irc
fe ($igetmatches(sample apple*)) sorted_index {
    echo $igetitem(sample $sorted_index)
}
```

## Source

Registered in [source/functions.c](../../source/functions.c). The `GET_MATCHES` wrapper, sorting, and array lookup: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h). Wildcard matching: [source/reg.c](../../source/reg.c).
