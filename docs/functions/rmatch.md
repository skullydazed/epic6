# rmatch function

## Synopsis

`$rmatch(<word> <pattern list>)`

## Technical

Tests each wildcard pattern against the first argument as literal text and returns the position of the best matching pattern, counting from **one**. The highest positive wildcard-match score wins; if scores tie, the first matching pattern wins. The return is a position, not the pattern itself or its score.

Scores generally reward explicitly matched literal bytes rather than text consumed by wildcards. They should not be treated as an exact count of non-wildcard characters for every pattern form. Matching is case-insensitive using byte-based C `tolower()` comparisons, rather than server nickname casemapping or Unicode case folding.

The word and patterns use normal function word parsing. With [/xdebug dword](../commands/xdebug.md) enabled, double quotes group text containing spaces and are removed before matching. With it disabled, quotes do not provide grouping. Each pattern tests the complete word, not an arbitrary substring; surrounding `*` wildcards can express a substring test.

Supported wildcard forms include `*` for any sequence of bytes, `%` for a sequence not crossing whitespace, `?` for one byte, and backslash escapes for literal characters. Escaped brackets can delimit alternative subpatterns. See [$match()](match.md) for the wildcard syntax; `match` reverses the argument roles by testing one pattern against a list of words. These are wildcard patterns, not regular expressions.

## Practical

Use this function to choose the most specific matching mask from a list, for example when comparing a userhost string against access-list masks. [$findw()](findw.md) is simpler for first-occurrence lookup in a literal word list. [$rpattern()](rpattern.md) returns all matching patterns rather than selecting one position.

Subtract one from a positive result before using it as a zero-based position with [$word()](word.md). Handle zero separately so that no match is not mistaken for the first item.

## Returns

`0` if no patterns match or the pattern list is empty; a positive one-based pattern position otherwise. Missing first-argument input returns the empty string. It does not change input variables, produce normal output, or require an IRC connection.

## Examples

| Expression | Result |
| --- | --- |
| `$rmatch(one o* t* f*)` | `1` |
| `$rmatch(one z* t* f*)` | `0` |
| `$rmatch(one o* on* t* f*)` | `2` |
| `$rmatch(one on* on*)` | `1`, the first tied match. |

## History

First appeared in ircII.

## Source

Registration and `function_rmatch()`: [source/functions.c](../../source/functions.c). Parsing: [include/words.h](../../include/words.h). Wildcard scores and comparisons: [source/reg.c](../../source/reg.c).
