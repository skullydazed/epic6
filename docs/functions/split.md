# $split()

## Synopsis

`$split(<chars> <text>)`

## Technical

Converts every character from the specified set found in the input text to one ordinary space. The set consists of individual Unicode codepoints, not a substring, wildcard pattern, or range expression. Matching is exact and case-sensitive.

A leading `^` inverts the set: characters not in the remaining set become spaces, while members remain. A caret elsewhere is literal. An empty set preserves valid input text; a set consisting only of `^` replaces every input codepoint with a space.

The character set is a double-quoted word, so quotes can group a set containing spaces regardless of `/xdebug dword`. The entire remaining text is processed, including spaces and literal quotation marks. Quotes in that text do not protect delimiters or group fields.

The result preserves existing whitespace and emits one space per replaced codepoint. Adjacent delimiters consequently produce adjacent spaces, and leading or trailing delimiters produce leading or trailing spaces. It does not normalize spacing or encode empty fields as quoted words. Nonreplaced codepoints are encoded back to UTF-8; malformed input bytes skipped by the decoder are not preserved.

## Practical

Use this to parse input with single-character delimiters, such as colon-separated fields. [$splitw()](splitw.md) is a separate interface for constructing a word list and has different output semantics. `split` returns transformed text without changing a variable.

## Returns

The input text with selected characters replaced. Missing input or empty text returns empty. There are no options, separate error codes, or IRC connection requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$split(: a:b:c)` | `a b c` |
| `$split(,. a,b.c)` | `a b c` |
| `$split(: a::b:)` | `a  b ` (two middle spaces and one trailing space) |
| `$split(^ab abc)` | `ab ` |
| `$split(é café)` | `caf ` |
| `$split("" a:b)` | `a:b` |

Registration and `function_split()`: [source/functions.c](../../source/functions.c). Double-quoted argument parsing: [include/words.h](../../include/words.h). UTF-8 decoding and encoding: [source/wcwidth.c](../../source/wcwidth.c).
