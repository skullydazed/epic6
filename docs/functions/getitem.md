# getitem function

## Synopsis

`$getitem(<array> <item number> [<item number> ...])`

## Technical

Retrieves text stored in a named array created by [$setitem()](setitem.md). Item numbers count from zero and identify storage positions, rather than sorted positions. [$igetitem()](igetitem.md) retrieves values by their indexes in sorted order instead.

The array name is case-insensitive. The name and item-number arguments are ordinary whitespace-separated words; double quotes do not group them, independently of `/xdebug dword`. Item numbers are converted with `strtol()` using base detection, accepting decimal, hexadecimal, and leading-zero octal notation. Nonnumeric input converts to zero, and a numeric prefix can be accepted with trailing nonnumeric text.

Multiple item numbers are processed in their supplied order. Valid values are appended to the result with a separating space; negative or out-of-range item numbers are skipped. Repeated numbers repeat the value. Returned values are not individually quoted, so values containing spaces do not retain distinct item boundaries in a multi-item result.

The function does not create an array, modify its contents, or sort its items. Item positions can change when preceding items are deleted; they are not permanent identifiers. These arrays are separate from `/assign` variable structures.

## Practical

Use this to read an array value at a known item number. Use `$igetitem()` to traverse values alphabetically, or [$finditem()](finditem.md) to locate an item number by an exact value.

## Returns

The requested item text, or multiple requested values concatenated with spaces. A missing array, missing item-number arguments, missing input, or a request containing no valid item number returns the empty string. A stored empty value can also return empty, so the result alone does not establish whether an item exists. Invalid numbers mixed with valid numbers do not produce placeholders or error messages.

## Examples

Given this array:

```irc
@ setitem(sample 0 zebra)
@ setitem(sample 1 apple pie)
```

| Expression | Result |
| --- | --- |
| `$getitem(sample 0)` | `zebra` |
| `$getitem(sample 1)` | `apple pie` |
| `$getitem(sample 1 0)` | `apple pie zebra` |
| `$getitem(sample 0 0)` | `zebra zebra` |
| `$getitem(sample -1 0 9)` | `zebra` |
| `$getitem(sample)` | Empty string |

## Source

Registered in [source/functions.c](../../source/functions.c). The `GETITEM` wrapper, array lookup, and item retrieval: [source/array.c](../../source/array.c). Numeric conversion and unquoted result concatenation: [source/ircaux.c](../../source/ircaux.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
