# $delitem()

## Synopsis

`$delitem(<array> <item#> [<item#> ...])`

## Technical

Deletes entries from a named array created with [`$setitem()`](setitem.md). Item numbers start at zero and refer to storage positions, not the array's sorted indexes. To convert a sorted index to an item number, use [`$indextoitem()`](indextoitem.md).

After each successful deletion, higher-numbered items immediately shift down by one to fill the gap. Multiple item numbers are processed from left to right against the already-modified array. Deleting its only remaining item deletes the array itself and stops processing further arguments.

The array name and item numbers are ordinary whitespace-separated words, without double-quote grouping, regardless of `/xdebug dword`. Array-name lookup is case-insensitive. Item numbers use `strtol()` with base zero: decimal, leading-zero octal, and `0x` hexadecimal forms are accepted. Numeric prefixes are accepted, and nonnumeric words convert to zero, so validate generated item arguments before deleting.

## Practical

Use this function to remove individual items while retaining the rest. To delete several original positions, process them in descending order so earlier deletions do not change the later targets, or consider [`$delitems()`](delitems.md), which uses different batch-deletion behavior. [`$delarray()`](delarray.md) deletes the entire named array.

## Returns

| Value | Meaning |
| --- | --- |
| `0` | At least one item was deleted. |
| `-2` | The array exists, but no supplied item number was valid, including no item arguments. |
| `-1` | The array does not exist, or no array name was supplied. |

Invalid negative or out-of-range numbers are skipped. A mixture of valid and invalid numbers still returns `0` if any deletion succeeds; the operation does not roll back earlier deletions or emit an error diagnostic.

## Examples

| Expression | Effect |
| --- | --- |
| `$delitem(array 2)` | Delete item 2 if it exists. |
| `$delitem(fake_array 4)` | Return `-1` if `fake_array` does not exist. |
| `$delitem(array -2)` | Return `-2` if `array` exists; no deletion. |
| `$delitem(array 1 1)` | Delete original items 1 and 2, if both exist, because the first deletion shifts item 2 down. |

## Source

Registered in [source/functions.c](../../source/functions.c). Deletion, compaction, sorted-index maintenance, and array lookup are in [source/array.c](../../source/array.c); numeric conversion is in [source/ircaux.c](../../source/ircaux.c).
