# pattern function

## Synopsis

`$pattern(<pattern> <word list>)`

## Technical

Returns all words in the list that match `pattern`, preserving their order, spelling, and duplicates. Matching uses EPIC's case-insensitive wildcard matcher, rather than regular expressions. It tests each complete word; use surrounding `*` wildcards to test for a substring. Unlike [$match()](match.md), this function keeps every matching word instead of selecting the best match.

This is the complement of [$filter()](filter.md). [$rpattern()](rpattern.md) reverses the argument roles: it tests a list of patterns against one fixed word.

The pattern and list use normal function word parsing. Double quotes group words containing spaces when [/xdebug dword](../commands/xdebug.md) is enabled; surrounding quotes are removed for matching, and returned words containing spaces are quoted again to preserve their grouping. Original quoting and separator spacing are not preserved. With that setting disabled, quotes do not provide this grouping. A pattern containing a literal space cannot match a word that contains no space.

Missing input, an empty word list, or a list with no matches returns the empty string. Empty quoted words are omitted from the returned list even if they match. The function returns a new list and does not change a variable whose value was supplied as input.

## Practical

Use this function to keep matching words from a list. Appending `*` to a prefix selects all words beginning with that prefix; matching is case-insensitive.

## Returns

A space-separated list of matching words.

## Examples

| Expression | Result |
| --- | --- |
| `$pattern(*oo* foobar blah booya)` | `foobar booya` |
| `$pattern(*a* hello there bob)` | Empty string. |
| `$pattern(irc* $myservers())` | Listed server names beginning with `irc`. |
| `$pattern(a* Alice bob ALAN Alice)` | `Alice ALAN Alice` |
| `$pattern("* *" "hello there" bob)` | `"hello there"`, with `/xdebug dword` enabled. |

## History

First appeared in plus-2, after ircII and before EPIC.

## Source

Registration and `function_pattern()` using `wrapper_pattern()` in inclusion mode: [source/functions.c](../../source/functions.c). Function word parsing: [include/words.h](../../include/words.h). Result quoting: [source/ircaux.c](../../source/ircaux.c). Wildcard matching: [source/reg.c](../../source/reg.c).
