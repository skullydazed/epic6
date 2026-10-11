# $count()

## Synopsis

`$count(<string> <text>)`

## Technical

Counts occurrences of `<string>` in `<text>`, including overlapping occurrences. Matching is case-insensitive substring matching, not whole-word or wildcard matching. For example, counting a nickname can also match it inside a longer word.

The string argument is always parsed as a double-quoted word. Surround it with double quotes when it contains spaces; those surrounding quotes are removed. This grouping does not depend on `/xdebug dword`. The remaining input is the text to search, rather than a list of separate arguments; quotes in that text are ordinary characters.

## Practical

Use this function to count occurrences of a short string in text, such as appearances of a person's nickname in a public message for logging purposes. Overlap matters: `zz` occurs three times in `zzzz`.

## Returns

Returns a decimal integer count. No matches, empty search text, or an explicitly empty search string return `0`. Missing input returns an empty string.

## Restrictions

Counts involving multibyte UTF-8 are unreliable in the current implementation. The search helper and counting loop mix byte lengths with code-point positions, which can miss matches or count a match more than once. Use ASCII input when an accurate count is required. The function does not modify variables supplying the input.

## Examples

| Expression | Result |
| --- | --- |
| `$count(. one.two.three)` | `2` |
| `$count(zz zzzz)` | `3` |
| `$count(Bob bob BOB bobby)` | `3` |
| `$count("a b" a b a b)` | `2` |
| `$count(x)` | `0` |
| `$count("" text)` | `0` |
| `$count()` | Empty string |

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Substring searching and case comparison are in [source/ircaux.c](../../source/ircaux.c); double-quoted argument parsing is in [source/words.c](../../source/words.c) and [include/words.h](../../include/words.h).
