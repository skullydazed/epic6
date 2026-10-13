# $rpattern()

## Synopsis

`$rpattern(<word> <pattern list>)`

## Technical

Returns a space-separated list of all patterns that match the fixed word, preserving their order, spelling, and duplicates. Matching uses EPIC's case-insensitive, byte-based wildcard matcher and tests the complete word. Wildcard characters in the first argument are treated as text being matched; each subsequent argument supplies a pattern. See [$match()](match.md) for wildcard syntax and limitations.

This is the complement of [$rfilter()](rfilter.md), which keeps nonmatching patterns, and the inverse of [$pattern()](pattern.md), which tests one pattern against a list of words. Unlike [$rmatch()](rmatch.md), it returns every matching pattern rather than the position of the best match.

The word and patterns use normal function word parsing. Double quotes group words containing spaces when [/xdebug dword](../commands/xdebug.md) is enabled. Quotes are removed for matching, and returned patterns containing literal spaces are quoted again with embedded double quotes escaped. Original quote spelling and separator spacing are not preserved. With DWORD disabled, quotes do not provide grouping.

Missing input, an empty pattern list, or no matching patterns returns the empty string. Empty quoted patterns are omitted from output even if they match an empty word. The function returns a new list without changing an input variable.

## Practical

Use this function to filter a pattern list through a word, keeping matching patterns and discarding nonmatching ones. For example, given masks and a `nick!user@host` string, it identifies the masks that match that string.

## Returns

A space-separated list of matching patterns. No IRC connection is required.

## Examples

| Expression | Result |
| --- | --- |
| `$rpattern(foobar f* *oo* a* *c)` | `f* *oo*` |
| `$rpattern(hello hello a* *lo*)` | `hello *lo*` |
| `$rpattern(HELLO h* h* a*)` | `h* h*` |
| `$rpattern(user@host.com *@* user@* f*@*.com)` | `*@* user@*` |
| `$rpattern(hello a* z*)` | Empty string |

## History

First appeared in plus-2, after ircII and before EPIC.

## Source

Registration and `function_rpattern()` using `wrapper_rpattern()` in inclusion mode: [source/functions.c](../../source/functions.c). Word parsing: [include/words.h](../../include/words.h). Result quoting: [source/ircaux.c](../../source/ircaux.c). Wildcard matching: [source/reg.c](../../source/reg.c).
