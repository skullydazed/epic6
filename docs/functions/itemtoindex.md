# $itemtoindex()

## Synopsis

```text
$itemtoindex(<array> <item number> [<item number> ...])
```

## Technical

Converts stored array item numbers to their positions in the array's sorted view. [indextoitem](indextoitem.md) performs the inverse conversion.

Item numbers are zero-based positions in the stored array. Appending to an array of five items adds item `5`; deleting items can shift the remaining item numbers. Sorted index numbers are also zero-based, but represent the ordering of item values.

The sorted view uses case-sensitive byte comparison (`strcmp`), with identical values ordered by item number. For ASCII text, uppercase letters sort before lowercase letters. This differs from the case-insensitive ordering of [sort](sort.md). The function rebuilds a pending sorted view when needed without rearranging the stored items.

The array name and item numbers are ordinary argument words. Array names are case-insensitive. Each number uses base-detecting integer conversion: decimal, hexadecimal, and leading-zero octal are accepted; nonnumeric text converts to zero. Multiple numbers produce space-separated results in the order supplied, including repeats and `-2` placeholders for invalid numbers.

## Practical

Use this with [igetitem](igetitem.md) or [ifinditem](ifinditem.md) when translating between stored item positions and sorted array access. Sorted positions can change when values are added, removed, or replaced.

## Returns

| Result | Meaning |
| --- | --- |
| Nonnegative integer | Sorted index corresponding to the supplied item number. |
| `-2` | Item number is negative or outside the array, or a known array was supplied without any item numbers. |
| `-1` | Array name is missing or unknown. |

With multiple item numbers, each result occupies its corresponding position in the returned list. A missing or unknown array returns a single `-1`.

## Examples

After `$setitem(booya 0 hello)` and `$setitem(booya 1 goodbye)`, the sorted view is `goodbye hello`:

| Expression | Result |
| --- | --- |
| `$itemtoindex(booya 0)` | `1` |
| `$indextoitem(booya 1)` | `0` |
| `$itemtoindex(booya 1 100 0)` | `0 -2 1` |
| `$itemtoindex(booya)` | `-2` |
| `$itemtoindex(unknown_array 0)` | `-1` if that array does not exist |

## Source

Registered in [source/functions.c](../../source/functions.c). Implemented by the `I2I` macro, `find_index`, and sorted-index helpers in [source/array.c](../../source/array.c). Numeric conversion is in [source/ircaux.c](../../source/ircaux.c).
