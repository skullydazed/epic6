# getarrays function

## Synopsis

`$getarrays([<pattern>])`

## Technical

Returns the names of existing named arrays created by [$setitem()](setitem.md). With no input, it returns all array names. Optional input is an EPIC case-insensitive wildcard pattern matched against each complete name; this is not a plain prefix filter or a regular expression. Use `prefix*` to select names beginning with a prefix.

The entire input is used as the pattern, without splitting it into words or removing surrounding quotes. Additional text is part of the pattern, rather than an ignored extra argument. `/xdebug dword` does not change this parsing.

Names are returned in uppercase, in the array registry's sorted name order. These are named arrays, separate from `/assign` variable structures. The function does not display the names or alter any array.

## Practical

Use this to discover arrays when a script needs to inspect or modify several of them without knowing their names in advance. See [$numarrays()](numarrays.md) for the number of arrays and [$numitems()](numitems.md) for an array's item count. Deleting an array removes its name from subsequent results.

## Returns

A space-separated list of matching names, or the empty string when there are no arrays or no names match. No input selects all arrays; a nonempty pattern that matches nothing also returns empty.

## Examples

Given arrays named `alpha`, `beta`, and `alpine`:

| Expression | Result |
| --- | --- |
| `$getarrays()` | `ALPHA ALPINE BETA` |
| `$getarrays(al*)` | `ALPHA ALPINE` |
| `$getarrays(beta)` | `BETA` |
| `$getarrays(al)` | Empty string, because no complete name equals `al`. |

```irc
fe ($getarrays()) array_name {
    echo $array_name has $numitems($array_name) items
}
```

## Source

Registered in [source/functions.c](../../source/functions.c). Name registration, sorted indexing, and `function_getarrays()`: [source/array.c](../../source/array.c). Wildcard matching: [source/reg.c](../../source/reg.c).
