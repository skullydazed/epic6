# $common()

## Synopsis

`$common(<word list 1> / <word list 2>)`

## Technical

A literal slash (`/`) separates the two word lists. Spaces around it are optional. The first slash in the input is used, even if it appears inside a quoted word; there is no quoting or escaping mechanism to protect that separator. If no slash is present, the function returns an empty string.

Returns words found in both lists, comparing whole words case-insensitively rather than using wildcard or substring matching. Words appear in the order encountered in the first list, with that list's spelling and capitalization.

Repeated words are not treated as a mathematical set. For each word in the first list, every still-unused matching occurrence in the second list adds a copy to the result and is consumed. Consequently, duplicates in the second list can produce repeated output, while later duplicate words in the first list cannot reuse those matches.

Whitespace between returned words is normalized to single spaces. Double-quoted word grouping is honored when `/xdebug dword` is enabled; returned words containing spaces are quoted again in that mode. With it disabled, double quotes do not group words.

## Practical

Use this function to find words shared by two lists. For example, given the nickname lists of two channels, it can find people present in both. Its comparison is EPIC's general case-insensitive string comparison, not the IRC server's nickname casemapping.

## Returns

Returns the common words as a space-separated list. Returns an empty string when either list is empty, there are no matches, or the separator is missing. Input variables used to supply the lists are not modified.

## Examples

| Expression | Result |
| --- | --- |
| `$common(one two three / two three four)` | `two three` |
| `$common(one two / three four)` | Empty string |
| `$common(One two / TWO one)` | `One two` |
| `$common(one / one one)` | `one one` |
| `$common(one one / one)` | `one` |

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Word splitting, result quoting, and case-insensitive comparison are in [source/ircaux.c](../../source/ircaux.c), with word parsing in [source/words.c](../../source/words.c).
