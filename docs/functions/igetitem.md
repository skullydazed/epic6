# igetitem function

## Synopsis

`$igetitem(<array> <index> [<index> ...])`

## Technical

Fetches data from a named array by its sorted index. Values sort case-sensitively, with equal values ordered by storage item number. Index zero is the first value; the last index is [$numitems()](numitems.md) minus one. [$getitem()](getitem.md) instead uses the storage item numbers assigned by [$setitem()](setitem.md).

Arrays normally retain their sorted view. Operations such as [$delitems()](delitems.md) and [$usetitem()](usetitem.md) can leave deferred sorting; `$igetitem()` rebuilds that sorted index before access. It does not change stored values or reorder their storage item numbers. Changes to values or membership can change sorted indexes, so an index is not a permanent identifier.

The array name is case-insensitive. Names and index arguments are ordinary whitespace-separated words; double quotes do not group them, independently of `/xdebug dword`. Indexes use base-detecting integer conversion, accepting decimal, hexadecimal, and leading-zero octal. Nonnumeric words convert to zero, and conversion can accept a numeric prefix with trailing text.

Multiple indexes are processed in their supplied order. Negative or out-of-range indexes are skipped; repeated indexes repeat the value. Selected text is concatenated with spaces without quoting individual values, so multiword values lose distinct boundaries in a multiple-index result. Invalid indexes produce no placeholders or diagnostics.

## Practical

Use this function to display an array in sorted order or retrieve a sorted index returned by [$ifinditem()](ifinditem.md) or [$ifindfirst()](ifindfirst.md). These named arrays are separate from `/assign` structures.

## Returns

The selected item text, or multiple values concatenated with spaces. Missing input, an unknown array, omitted indexes, or no valid indexes returns empty. A stored empty value can also return empty, so an empty result does not establish whether the index exists.

## Examples

```irc
@ setitem(sample 0 zebra)
@ setitem(sample 1 apple pie)
```

| Expression | Result |
| --- | --- |
| `$igetitem(sample 0)` | `apple pie` |
| `$igetitem(sample 1)` | `zebra` |
| `$igetitem(sample 1 0)` | `zebra apple pie` |
| `$igetitem(sample -1 0 9)` | `apple pie` |
| `$igetitem(sample)` | Empty string |

## History

First appeared in “plus 3” (post-ircII, pre-EPIC).

## Source

Registered in [source/functions.c](../../source/functions.c). The `GETITEM` wrapper, sorted-index rebuilding, tie ordering, and array lookup: [source/array.c](../../source/array.c). Numeric conversion and unquoted result concatenation: [source/ircaux.c](../../source/ircaux.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
