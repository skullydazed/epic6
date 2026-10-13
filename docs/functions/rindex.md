# rindex function

## Synopsis

`$rindex(<characters> <text>)`

## Technical

Returns the zero-based position of the last code point in text that belongs to the supplied character set. Comparisons are exact and case-sensitive. The set is a list of individual characters, not a substring, wildcard pattern, regular expression, or range notation.

The character-set argument is a double-quoted word, so quotes can include spaces regardless of `/xdebug dword`. To include a literal double quote without starting a quoted argument, put another set character before it. A leading caret `^` negates the set: search for the last character not among those following the caret. A set consisting only of `^` selects any character; put a caret later in the set to match it literally.

After the set argument, the remaining text is used without further word splitting or quote removal. Only the first separating whitespace character is consumed, so additional leading spaces in text count toward the returned position.

Positions count Unicode code points from the beginning of the text, even though searching proceeds backward. They are not byte offsets, display columns, or grapheme counts. [$index()](index.md) searches for the first matching position instead.

## Practical

Use this function to locate the final occurrence of a character from a set. [$before()](before.md) and [$after()](after.md) provide more direct extraction around delimiters when a numeric position is unnecessary.

## Returns

The position as a decimal integer string, or `-1` for no match, an empty character set, or empty text. Missing input returns the empty string. It does not change the input variable, produce normal output, or require an IRC connection.

## Restrictions

Supply valid UTF-8. Backward decoding and the quick code-point index helper are not a complete validation step, so malformed input can produce inconsistent positions. Visually identical characters with different Unicode compositions are not normalized before comparison.

## Examples

| Expression | Result |
| --- | --- |
| `$rindex(abc hello there bob)` | `14` |
| `$rindex(abc   hello there bob)` | `16`, because two additional spaces belong to text. |
| `$rindex(xyz hello there bob)` | `-1` |
| `$rindex(^a banana)` | `4`, the final `n`. |
| `$rindex(é caféé)` | `4` |
| `$rindex()` | Empty string. |

## History

First appeared in ircII-2.1.3.

## Source

Registration and `function_rindex()`: [source/functions.c](../../source/functions.c). Inversion, matching, and position conversion in `rcpindex2()`: [source/ircaux.c](../../source/ircaux.c). Backward Unicode decoding and quick indexing: [source/wcwidth.c](../../source/wcwidth.c).
