# $match()

## Synopsis

`$match(<pattern> <word list>)`

## Technical

Matches a wildcard pattern against zero or more words of literal text and returns the position of the best match, counting from **one**. The highest positive wildcard-match score wins; when scores tie, the first matching word wins. For ordinary patterns without alternatives, successful matches normally have the same score, so this commonly returns the first match. The returned number is a word position, not the score.

Both the pattern and the list use normal function word parsing. With [/xdebug dword](../commands/xdebug.md) enabled, double-quoted text containing spaces counts as one word, and surrounding quotes are removed before matching. With it disabled, whitespace separates words and quotation marks are ordinary characters.

The matcher compares complete words case-insensitively using byte-based C `tolower()` comparisons. It does not use the server's nickname case mapping or Unicode case folding. Supported wildcard syntax includes:

| Pattern | Meaning |
| --- | --- |
| `*` | Any sequence of bytes, including whitespace |
| `%` | A sequence that does not cross whitespace |
| `?` | One byte |
| `\x` | Literal `x` |
| `\[foo bar\]` | Alternative subpatterns `foo` or `bar` |

Alternative subpatterns are expanded and their best successful score is used. Scores generally reward explicitly matched literal bytes rather than text consumed by wildcards; a longer candidate does not inherently win. This is wildcard matching, not regular expression matching. Ordinary square brackets do not define a character class.

## Practical

Use this function to locate a wildcard match in a word list. Other word functions, such as [$word()](word.md), count from zero: check that `$match()` returned a positive value, then subtract one before using its result as a word index.

For a literal word lookup, use [$findw()](findw.md). For a boolean match of one complete string, the `=~` operator avoids constructing a word list. [$rmatch()](rmatch.md) instead matches one text word against a list of patterns.

## Returns

- Empty string when the pattern argument is missing.
- `0` when no candidate matches, including an empty candidate list.
- A positive, one-based position of the highest-scoring match, with the first position winning ties.

The input list is not modified. Malformed patterns have no separate error result. The matcher has an iteration guard that can reject particularly complex matches with a diagnostic.

## Examples

| Expression | Result |
| --- | --- |
| `$match(*oo* blah foo booya)` | `2` |
| `$match(*oo* blah fubar erf)` | `0` |
| `$match(foo* foo foobar)` | `1` (tied scores) |
| `$match(FOO blah foo)` | `2` |
| `$match(foo)` | `0` |
| `$match()` | Empty string |

With `xdebug dword` enabled, `$match("*two words*" one "two words" three)` returns `2`. `$match("\[foo foobar\]" foo foobar)` also returns `2`: the longer literal alternative has the higher score.

## History

This function first appeared in ircII 2.1.5.

Registration and `function_match()`: [source/functions.c](../../source/functions.c). Wildcard matching and scoring: [source/reg.c](../../source/reg.c). Function word parsing: [include/words.h](../../include/words.h) and [source/ircaux.c](../../source/ircaux.c). The `=~` operator: [source/expr2.c](../../source/expr2.c).
