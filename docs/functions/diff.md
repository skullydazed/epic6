# $diff()

## Synopsis

`$diff(<word list 1> / <word list 2>)`

## Technical

A literal slash (`/`) separates the two word lists. Spaces around it are optional. The first slash is used even if it appears inside a quoted word; quoting or escaping cannot protect it from being the separator. Missing the slash returns an empty string.

For lists without repeated words, returns words present in one list but not both: their symmetric difference. Comparisons are case-insensitive whole-word comparisons, not wildcard or substring matching. The result lists unmatched words from the first list in their original order, followed by unmatched words from the second list in their original order, preserving each returned word's spelling.

Duplicates are not treated as a mathematical set. Each first-list word consumes every still-unused matching occurrence in the second list. If it consumes none, that first-list word is returned. Afterward, all unconsumed second-list words are returned. Thus later duplicates in the first list can survive after the first occurrence consumes all counterparts, and swapping the lists can change more than output order.

Whitespace between returned words is normalized to single spaces. Double-quoted word grouping is honored when `/xdebug dword` is enabled; returned words containing spaces are quoted again in that mode. With it disabled, double quotes do not group words.

## Practical

Use this function to find the uncommon elements of two lists. [`$common()`](common.md) selects common words using related matching and duplicate-consumption rules. These comparisons use EPIC's general case-insensitive string comparison, not IRC server nickname casemapping.

## Returns

Returns a space-separated list, or an empty string if no words remain or the separator is missing. An empty list on one side leaves the other side's words as the result. Input variables supplying the lists are not modified.

## Examples

| Expression | Result |
| --- | --- |
| `$diff(one two three / two three four)` | `one four` |
| `$diff(one two / three four)` | `one two three four` |
| `$diff(One two / TWO three)` | `One three` |
| `$diff(one one / one)` | `one` |
| `$diff(one / one one)` | Empty string |

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Word splitting, result quoting, and case-insensitive comparison are in [source/ircaux.c](../../source/ircaux.c), with word parsing in [source/words.c](../../source/words.c).
