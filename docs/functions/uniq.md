# uniq

## Synopsis

`$uniq(<word list>)`

## Technical

Returns the words in `<word list>` with duplicates removed. The first occurrence of each word is retained, including its original spelling and case, and the result preserves that order. Duplicate words need not be adjacent; the result is not alphabetically sorted.

Comparison is literal and case-insensitive, using EPIC's Unicode uppercase mapping when its dedicated locale is available. It does not use wildcard matching, server nickname case mappings, full Unicode case folding, or normalization. Without the dedicated locale, non-ASCII code points are compared unchanged.

Parsing and output grouping honor [`xdebug dword`](../commands/xdebug.md). When enabled, a double-quoted phrase counts as one word and is dequoted for comparison. When disabled, whitespace separates words and quotation marks are ordinary characters.

## Practical

Use this function to remove repeated entries while preserving the order of their first appearance. [`sort`](sort.md) instead orders words without removing duplicates.

The current implementation sorts temporarily to identify duplicates, then restores original order. It does not use the older quadratic repeated-search algorithm.

## Returns and restrictions

A word list with duplicates removed, or empty for missing or empty input. Separator whitespace is rebuilt as single spaces. With quoted-word mode enabled, words containing spaces are quoted again as needed; original quoting and escaping are not preserved exactly. Empty words are omitted from output. No variables are changed, and there are no options or server requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$uniq(one two three two one)` | `one two three` |
| `$uniq(one    two three  two one)` | `one two three` |
| `$uniq(blah blah blah)` | `blah` |
| `$uniq(One two ONE Two)` | `One two` |

With `xdebug dword` enabled, `$uniq("two words" one "TWO WORDS")` returns `"two words" one`.

## History

First appeared in EPIC3.001.

## Source

Handler: [`source/functions.c`](../../source/functions.c:5368). Comparison: [`sort_it`](../../source/functions.c:5162). List reconstruction: [`unsplitw`](../../source/ircaux.c:3209).
