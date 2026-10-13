# $setitem()

## Synopsis

`$setitem(<array> <item number> [data])`

## Technical

Creates or updates a cell in a named array. If the array does not exist, item number `0` creates it. In an existing array, an item number below its current size replaces that item's value; an item number equal to the size appends one item. Larger numbers fail: arrays do not acquire gaps. Storage item numbers start at zero.

The array name and item number are ordinary, unquoted words, independent of `/xdebug dword`. Array names are case-insensitive and stored uppercase; spaces cannot be grouped into a name with quotes. The complete remaining text is the data, including spaces and literal quotation marks. Data spelling and case are preserved. Omitted data stores an empty string rather than deleting the cell.

Item-number parsing is permissive: nonnumeric text becomes zero and numeric prefixes can be accepted. Supply an ordinary nonnegative decimal number for an intentional index. Negative numbers return `-1` before looking up the array.

## Practical

This is the usual doorway into EPIC's named arrays. They are separate from [/assign](../commands/assign.md) structures and persist in the client's array registry until removed. [$usetitem()](usetitem.md) is another insertion interface that defers sorted-index maintenance.

Use [$numitems()](numitems.md) to obtain the next append item number for an existing array. Use [$getitem()](getitem.md) to read by storage item number. Replacing a value does not move its storage item number, but can change its sorted index position. The implementation maintains or marks the separate sorted index for later rebuilding; item numbers and sorted index numbers are different.

To remove cells or an entire array, use [$delitem()](delitem.md), [$delitems()](delitems.md), or [$delarray()](delarray.md), rather than assigning empty data.

## Returns

| Result | Meaning |
| --- | --- |
| `-2` | Existing array, but item number is greater than its size |
| `-1` | Missing name or item number, negative item number, or nonexistent array with an item number other than zero |
| `0` | Existing item overwritten |
| `1` | New array created with item zero |
| `2` | New item appended to an existing array |

There are no options or IRC connection requirements. The returned integer describes the operation; it is not the assigned data or item number.

## Examples

Starting with no array named `foo`, in this order:

| Expression | Result and effect |
| --- | --- |
| `$setitem(foo 1 blah)` | `-1`; no array created |
| `$setitem(foo 0 blah blah)` | `1`; create item zero |
| `$setitem(foo 1 fubar booya)` | `2`; append item one |
| `$setitem(foo 5 booya)` | `-2`; no change |
| `$setitem(foo 0 ha ha)` | `0`; overwrite item zero |
| `$setitem(foo 0)` | `0`; item zero now contains empty text |

Registration: [source/functions.c](../../source/functions.c). `FUNCTION_SETITEM`, `set_item()`, and array/index maintenance: [source/array.c](../../source/array.c). Ordinary word parsing: [include/words.h](../../include/words.h). Numeric conversion: [source/ircaux.c](../../source/ircaux.c).
