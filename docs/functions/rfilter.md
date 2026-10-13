# rfilter function

## Synopsis

`$rfilter(<word> <pattern list>)`

## Technical

Returns every pattern in the list that does **not** match the fixed word, preserving pattern order, spelling, and duplicates. Matching uses EPIC's case-insensitive wildcard matcher, not regular expressions, and tests the complete word. Wildcard characters in the first argument are treated as text being matched; each subsequent argument supplies a pattern.

This is the complement of [$rpattern()](rpattern.md). [$filter()](filter.md) reverses the argument roles by testing one pattern against a list of words.

The word and patterns use normal function word parsing. Double quotes group words containing spaces when [/xdebug dword](../commands/xdebug.md) is enabled. Quotes are removed for matching, and returned patterns containing literal spaces are quoted again with embedded double quotes escaped. Original quote spelling and separator spacing are not preserved. With DWORD disabled, quotes do not provide grouping.

Missing input, an empty pattern list, or a list whose patterns all match returns the empty string. Empty quoted patterns are omitted from output even when they do not match. The function returns a new list without changing an input variable.

## Practical

Use this function to remove matching masks from a pattern list, for example masks matching a particular `nick!user@host` string. It keeps the patterns that fail to match that word.

## Returns

A space-separated list of nonmatching patterns. No IRC connection is required.

## Examples

| Expression | Result |
| --- | --- |
| `$rfilter(foobar f* *oo* a* *c)` | `a* *c` |
| `$rfilter(hello hello a* *lo*)` | `a*` |
| `$rfilter(HELLO h* a* a*)` | `a* a*` |
| `$rfilter(user@host.com *@* user@* f*@*.com)` | `f*@*.com` |

## History

First appeared in plus-2, after ircII and before EPIC.

## Source

Registration and `function_rfilter()` using `wrapper_rpattern()` in exclusion mode: [source/functions.c](../../source/functions.c). Word parsing: [include/words.h](../../include/words.h). Result quoting: [source/ircaux.c](../../source/ircaux.c). Wildcard matching: [source/reg.c](../../source/reg.c).
