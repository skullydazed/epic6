# $numsort()

## Synopsis

`$numsort(<list>)`

## Technical

Sorts a word list using numeric comparison for integer portions, including numbers embedded within words. Other portions compare case-insensitively using byte-based C `tolower()` comparisons. This is useful for names such as `item2` and `item10`, as well as lists of integers.

The comparator walks corresponding portions of the words. When it encounters a digit, or `+` or `-` followed by a digit, it converts numeric prefixes with base detection: decimal, hexadecimal `0x`, and leading-zero octal are recognized. Unequal numeric values determine the order; equal values continue comparison with the following text. A nonnumeric prefix converts to zero without consuming text, which puts ordinary alphabetic words between zero and positive integer words in typical mixed lists.

Use integral values, not floating-point numbers. A decimal point is ordinary text separating integer portions, so this is not a floating-point numeric sort. Unicode digits and Unicode case folding are not supported by this comparator.

Word grouping honors [/xdebug dword](../commands/xdebug.md). With it enabled, a double-quoted phrase is one sortable word. The result is rebuilt with single spaces between nonempty words and quoting where needed to preserve grouped words; original separator spacing is not preserved. Empty words are omitted from the rebuilt result.

## Returns

The sorted list, or the empty string for an empty list. The source variable is not changed. Repeated nonempty words are retained. Words that compare equal, including differences only in case or equivalent integer spellings with the same suffix, have no guaranteed relative order because the sort is not stable.

## Restrictions

The comparator subtracts converted integers and returns the difference as a C `int`, rather than comparing their signs safely. Large numeric differences, integer overflow, or out-of-range numeric text can therefore produce incorrect ordering. Use modest integer values when reliable numeric ordering matters. There is no separate error result for these cases.

## Examples

| Expression | Result |
| --- | --- |
| `$numsort(4 -6 34 -96 0)` | `-96 -6 0 4 34` |
| `$numsort(4 -6 34 -96 0 Erf blah)` | `-96 -6 0 blah Erf 4 34` |
| `$numsort(boox boo1 boo0 boo-4)` | `boo-4 boo0 boox boo1` |
| `$numsort(item10 item2 item1)` | `item1 item2 item10` |
| `$numsort(0x10 9 010)` | `010 9 0x10` |
| `$numsort()` | Empty string |

For a case-insensitive text sort, see [$sort()](sort.md); [$sortby()](sortby.md) supplies a custom comparison.

Registration, `function_numsort()`, and `num_sort_it()`: [source/functions.c](../../source/functions.c). Numeric-start detection: [include/ircaux.h](../../include/ircaux.h). Word-list splitting and rebuilding: [source/ircaux.c](../../source/ircaux.c).
