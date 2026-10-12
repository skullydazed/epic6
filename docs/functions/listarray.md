# $listarray()

## Synopsis

`$listarray(<array> [<separator>])`

## Description

Converts a named array's items into one combined string, inserting the separator between nonempty items. Items are visited in stored item-number order, not sorted-index order. The source array is not changed.

The array name is an ordinary argument word and is matched case-insensitively. The separator defaults to one space. When supplied, it is always parsed as a double-quoted word, regardless of `xdebug dword`, so `" | "` can specify a separator containing spaces and `""` can concatenate items without a separator. Additional arguments are ignored.

Each item is appended as its complete stored text, without splitting, trimming, dequoting, escaping, or adding protective quotes. Thus an item containing several words becomes part of the combined word list when the separator is a space. Empty items are skipped completely, with no placeholder or separator for them.

## Practical

With the default separator, this collapses item word lists into one ordinary word list. Like [unsplit](unsplit.md), it joins values with a chosen separator, but reads the values directly from an array rather than a quoted-word list. It is not a lossless representation of item boundaries when item contents include the separator or items are empty.

## Returns

The combined item text, or the empty string for a missing or unknown array, or when every item is empty. There is no separate error sentinel.

## Examples

For an array whose stored items are `hello there`, an empty string, and `bob`:

| Expression | Result |
| --- | --- |
| `$listarray(example)` | `hello there bob` |
| `$listarray(example ,)` | `hello there,bob` |
| `$listarray(example " | ")` | `hello there | bob` |
| `$listarray(example "")` | `hello therebob` |

## Source

Registered in [source/functions.c](../../source/functions.c) and implemented as `function_listarray` in [source/array.c](../../source/array.c). Separator parsing and nonempty-text concatenation (`malloc_strcat_wordlist`) are in [source/ircaux.c](../../source/ircaux.c).
