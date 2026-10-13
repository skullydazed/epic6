# $numitems()

## Synopsis

`$numitems(<array>)`

## Technical

Returns the number of items currently defined in the named array. These are the named arrays created with [$setitem()](setitem.md), separate from `/assign` structures. Items with empty stored strings still count as items.

The array name is the first ordinary, unquoted word and is case-insensitive. Additional arguments are ignored. Quotation marks are not removed, and this parsing does not depend on `/xdebug dword`.

## Practical

Array storage item numbers run from zero through `count - 1`, so the returned count is the next item number that can be appended with `$setitem()`. Deleting items compacts storage item numbers; the count is not a permanent identifier or the total ever inserted. [$getitem()](getitem.md) retrieves a value by storage item number, and [$numarrays()](numarrays.md) counts arrays rather than items.

## Returns

The array's item count, or `0` if the array name is missing or the array does not exist. There is no `-1` error sentinel or diagnostic for a missing array. The call does not create an array or change its values or sorted indexes.

## Examples

After successfully storing items zero and one in `sample`, `$numitems(sample)` returns `2`; `$numitems(SAMPLE ignored)` returns the same count. An empty string stored in either item does not reduce the count.

`$numitems()` returns `0`. `$numitems(missing)` returns `0` when no array named `missing` exists.

Registration: [source/functions.c](../../source/functions.c). `function_numitems()`, array lookup, storage sizing, and item creation/deletion: [source/array.c](../../source/array.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
