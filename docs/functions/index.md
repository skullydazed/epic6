# index function

## Synopsis

`$index(<characters> <text>)`

## Technical

Returns the zero-based position of the first character in `text` belonging to the supplied character set. The set can contain any number of characters; it is not a substring, wildcard pattern, or regular expression. Matching is case-sensitive and compares decoded Unicode code points.

The character-set argument is always a double-quote-aware word, independently of `/xdebug dword`. Quote it to include spaces. To include a literal double quote, put another character before it so the word does not start with a quote.

If the first character in the set is `^`, the set is inverted: the function finds the first text character absent from the remaining set. A caret elsewhere is literal. An empty set matches nothing; a set consisting only of `^` matches the first character of any nonempty text.

Text is the entire remainder, with no surrounding-quote removal. Counting begins after the first separator following the set; additional separator spaces belong to the text and count toward the position. For valid UTF-8, positions count Unicode code points rather than bytes, display columns, or combined visual characters. Invalid UTF-8 is skipped while decoding, and positions for malformed text should not be treated as reliable Unicode indexes.

## Practical

Use this to find the first occurrence of any character in a set, or the first character outside a permitted set. For extracting text around a delimiter, [$before()](before.md) and [$after()](after.md) are often simpler. [$rindex()](rindex.md) searches from the other end.

## Returns

A nonnegative position when a matching character exists; zero is valid. Returns `-1` if nothing matches or text is empty. Missing the character-set argument returns the empty string. No diagnostic is printed.

## Examples

| Expression | Result |
| --- | --- |
| `$index(abc hello there bob)` | `12` |
| `$index(abc   hello there bob)` | `14` (two extra spaces are counted) |
| `$index(xyz hello there bob)` | `-1` |
| `$index(^0123456789 123x)` | `3` |
| `$index(" " hello there)` | `5` |
| `$index(é café)` | `3` |
| `$index()` | Empty string |

## History

Originally appeared in ircII.

## Source

Registration and wrapper: [source/functions.c](../../source/functions.c). Character-set inversion and matching: [source/ircaux.c](../../source/ircaux.c). Word boundaries and separator handling: [source/words.c](../../source/words.c). UTF-8 decoding and code-point position counting: [source/wcwidth.c](../../source/wcwidth.c).
