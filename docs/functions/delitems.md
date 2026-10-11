# $delitems()

## Synopsis

`$delitems(<array> <item#> [<item#> ...])`

## Technical

Deletes entries from a named array created with [`$setitem()`](setitem.md). Item numbers start at zero and refer to storage positions, not the array's sorted indexes. To convert a sorted index to an item number, use [`$indextoitem()`](indextoitem.md).

All item numbers refer to positions in the array as it was when the call began. Valid items are marked for deletion without immediate renumbering; repeated numbers for an already-deleted item are skipped. After the arguments are processed, remaining items retain their relative order and are compacted to consecutive numbers starting at zero. Deleting every item removes the array itself and stops processing further arguments.

After a partial deletion, the array is put into unsorted mode: its sorted index is rebuilt when an operation requiring sorted access, such as [`$igetitem()`](igetitem.md), next uses it. Ordinary [`$getitem()`](getitem.md) access does not trigger that sorting.

The array name and item numbers are ordinary whitespace-separated words, without double-quote grouping, regardless of `/xdebug dword`. Array-name lookup is case-insensitive. Item numbers use `strtol()` with base zero: decimal, leading-zero octal, and `0x` hexadecimal forms are accepted. Numeric prefixes are accepted, and nonnumeric words convert to zero, so validate generated item arguments before deleting.

## Practical

Use this function to remove several original positions in one call. It avoids the repeated compaction and deferred sorted-index rebuilding of [`$delitem()`](delitem.md), which processes deletions sequentially against shifted item numbers. [`$delarray()`](delarray.md) deletes the entire named array.

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
| `$delitems(array 2)` | Delete item 2 if it exists. |
| `$delitems(fake_array 4)` | Return `-1` if `fake_array` does not exist. |
| `$delitems(array -2)` | Return `-2` if `array` exists; no deletion. |
| `$delitems(array 1 1)` | Delete original item 1 once; the duplicate is ignored. |
| `$delitems(array 1 2)` | Delete original items 1 and 2, if both exist. |

## Source

Registered in [source/functions.c](../../source/functions.c). Deletion, compaction, deferred sorted-index rebuilding, and array lookup are in [source/array.c](../../source/array.c); numeric conversion is in [source/ircaux.c](../../source/ircaux.c).
