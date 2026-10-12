# $maxlen()

## Synopsis

`$maxlen(<word list>)`

## Technical

Returns the length of the longest word in the list. For well-formed UTF-8, length is measured in Unicode code points, not bytes, grapheme clusters, or terminal columns. Combining marks count separately, and a wide character still counts as one. IRC formatting and control characters are not stripped before counting.

Words use normal function word parsing. With [/xdebug dword](../commands/xdebug.md) enabled, a double-quoted phrase counts as one word, surrounding quotes are removed, and spaces within the phrase contribute to its length. With it disabled, whitespace separates words and quotation marks are ordinary characters.

## Practical

For plain ASCII words without formatting, the result can help determine a column width and how many columns fit on screen. For general terminal text, use [$printlen()](printlen.md) to measure display width instead; `$maxlen()` does not account for wide characters, combining marks, or formatting sequences.

## Returns

A nonnegative length. An omitted or empty word list returns `0`. The input list is not modified.

The quick counter does not validate UTF-8. It counts each byte below `0x80` or at least `0xC0` and ignores bytes in `0x80`–`0xBF`; results for malformed text are therefore not a reliable character count.

## Examples

| Expression | Result |
| --- | --- |
| `$maxlen()` | `0` |
| `$maxlen(one)` | `3` |
| `$maxlen(one two three four)` | `5` |
| `$maxlen(café)` | `4` |
| `$maxlen(界)` | `1` |

With `xdebug dword` enabled, `$maxlen(one "two words" three)` returns `9`.

Registration and `function_maxlen()`: [source/functions.c](../../source/functions.c). Quick code point counting: [source/wcwidth.c](../../source/wcwidth.c). Function word parsing: [include/words.h](../../include/words.h) and [source/ircaux.c](../../source/ircaux.c).
