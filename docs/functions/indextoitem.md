# indextoitem function

## Synopsis

`$indextoitem(<array> <index> [<index> ...])`

## Technical

Converts sorted indexes in a named array to their corresponding storage item numbers. [$itemtoindex()](itemtoindex.md) converts in the opposite direction.

Storage item numbers count from zero and are assigned when values are added with [$setitem()](setitem.md). Sorted indexes describe the values' case-sensitive text order, with equal text ordered by storage item number. For ASCII text, uppercase letters sort before lowercase letters. Sorting uses literal byte comparison rather than the case-insensitive ordering of [$sort()](sort.md).

The function rebuilds deferred sorted indexes before translating. It does not change stored values or storage item numbers. Changing array values or membership can change sorted indexes; deleting items can also shift storage item numbers.

The array name is case-insensitive. Names and indexes are ordinary whitespace-separated words; double quotes do not group them, independently of `/xdebug dword`. Indexes use base-detecting integer conversion: decimal, hexadecimal, and leading-zero octal are accepted. Nonnumeric words convert to zero; a numeric prefix can be accepted with trailing text.

Multiple indexes are processed in the supplied order, including duplicates. Each invalid index produces a `-2` placeholder rather than being skipped.

## Practical

Use this with [$igetitem()](igetitem.md) or [$ifinditem()](ifinditem.md) to work in sorted order while obtaining storage item numbers for other array operations. These named arrays are separate from `/assign` structures.

## Returns

One item number per supplied index, separated by spaces. A nonnegative number is a successful translation; zero is valid. Invalid indexes return `-2` in their corresponding positions. An existing array with no index arguments returns `-2`; an unknown array or missing array name returns `-1`. No diagnostic is printed.

## Examples

```irc
@ setitem(booya 0 hello)
@ setitem(booya 1 goodbye)
```

| Expression | Result |
| --- | --- |
| `$indextoitem(booya 0)` | `1` |
| `$indextoitem(booya 1)` | `0` |
| `$itemtoindex(booya 0)` | `1` |
| `$indextoitem(booya 1 100 0)` | `0 -2 1` |
| `$indextoitem(booya)` | `-2` |

## Source

Registered in [source/functions.c](../../source/functions.c). The `I2I` wrapper, array lookup, sorted-index rebuilding, and translation: [source/array.c](../../source/array.c). Numeric conversion: [source/ircaux.c](../../source/ircaux.c). Ordinary-word parsing: [include/words.h](../../include/words.h).
