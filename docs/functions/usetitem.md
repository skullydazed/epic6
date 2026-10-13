# usetitem

Create or replace a named-array cell while deferring sorted-index maintenance.

## Synopsis

```irc
$usetitem(<array> <item number> [data])
```

## Technical

This function has the insertion and replacement behavior of [setitem](setitem.md). Item numbers are zero-based storage positions. Item zero creates an absent array; an existing item number replaces its value; an item number equal to the array's size appends a cell. Larger numbers fail rather than creating gaps.

The array name and item number are ordinary unquoted words, independent of `/xdebug dword`. Names are case-insensitive and stored uppercase. The complete remaining text is the data, including spaces and literal quotes. Missing data stores an empty string; it does not delete the item. Missing name or item number returns `-1`. Item-number conversion accepts numeric prefixes and treats nonnumeric text as zero; negative numbers return `-1` before array lookup.

Unlike `setitem`, a successful change to an existing array always marks its separate sorted index for rebuilding instead of maintaining it immediately. A newly created array is also marked for rebuilding. Rebuilding occurs when an operation needs sorted indices, such as [igetitem](igetitem.md) or [finditem](finditem.md). Reading by storage item number with [getitem](getitem.md), or counting cells with [numitems](numitems.md), does not rebuild it. A later `setitem` on an array already marked unsorted also defers maintenance.

## Practical

Use this interface when adding many values before performing sorted lookups. Deferred sorting does not rearrange the stored cells or change their item numbers. Sorted index positions and storage item numbers are different. Named arrays are separate from `/assign` structures and remain in the array registry until removed.

## Returns

| Result | Meaning |
| --- | --- |
| `-2` | Existing array, but item number is greater than its size |
| `-1` | Missing name or item number, negative item number, or absent array with a nonzero item number |
| `0` | Existing item overwritten |
| `1` | New array created with item zero |
| `2` | New item appended to an existing array |

## Examples

Starting with no array named `sample`:

```irc
$usetitem(sample 0 zebra) /* 1: create item zero. */
$usetitem(sample 1 apple) /* 2: append item one. */
$getitem(sample 0)       /* zebra; does not require sorted indices. */
$igetitem(sample 0)      /* apple; rebuilds and reads the first sorted item. */
$usetitem(sample 0 pear) /* 0: replace storage item zero. */
```

## Source

Registered in [source/functions.c](../../source/functions.c). Implemented by `FUNCTION_SETITEM(function_usetitem, 1)`, `set_item`, and the sorted-index helpers in [source/array.c](../../source/array.c).
