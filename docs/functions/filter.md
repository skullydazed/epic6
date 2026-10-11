# filter function

## Synopsis

`$filter(<pattern> <word list>)`

## Technical

Returns the words in the list that do **not** match `pattern`, preserving their order, spelling, and duplicates. Matching uses EPIC's case-insensitive wildcard matcher, rather than regular expressions. It tests each complete word, not merely whether the pattern appears somewhere within it; use surrounding `*` wildcards for a substring test.

This is the complement of [$pattern()](pattern.md). [$rfilter()](rfilter.md) reverses the argument roles: it tests a list of patterns against one fixed word.

The pattern and list use normal function word parsing. Double quotes group words containing spaces when `/xdebug dword` is enabled; surrounding quotes are removed for matching, and returned words containing spaces are quoted again to preserve their grouping. With that setting disabled, quotes do not provide this grouping.

Missing input, an empty word list, or a list in which every word matches returns the empty string. The function returns a new list and does not change a variable whose value was supplied as input.

## Practical

Use this function to remove matching words from a list. Appending `*` to a prefix selects all words beginning with that prefix for removal; matching is case-insensitive.

## Returns

A space-separated list of the words left after matching words have been removed.

## Examples

| Expression | Result |
| --- | --- |
| `$filter(*oo* foobar blah booya)` | `blah` |
| `$filter(*a* hello there bob)` | `hello there bob` |
| `$filter(irc* $myservers())` | Listed servers whose names do not begin with `irc`. |
| `$filter(a* Alice bob ALAN bob)` | `bob bob` |
| `$filter(* hello there)` | Empty string. |

## History

First appeared in plus-2, after ircII and before EPIC.

## Source

Registration and `wrapper_pattern()` with exclusion mode: [source/functions.c](../../source/functions.c). Function word parsing: [include/words.h](../../include/words.h). Result quoting: [source/ircaux.c](../../source/ircaux.c). Wildcard matching: [source/reg.c](../../source/reg.c).
