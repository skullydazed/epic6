# rest function

## Synopsis

`$rest(<text>)`

`$rest(<position> <text>)`

## Technical

Returns text with its first `position` Unicode code points removed. The default position is `1`, removing the initial code point. Zero returns the entire text, while a positive position at or beyond its code-point length returns empty. These positions are not byte offsets, display columns, or grapheme counts.

A leading decimal integer followed by whitespace selects the explicit-position form. The position is then converted with normal integer argument parsing, which recognizes base prefixes such as a leading zero for octal. Otherwise the entire input is treated as text. A numeric input with no following whitespace, such as `123`, is text rather than an explicit position.

After consuming an explicit position, the remaining text is used without further word splitting or quote removal. If text may start with a number followed by whitespace, supply an explicit position to avoid mistaking that number for the position. There is no JSON argument form.

Negative positions select a suffix by walking back from the end: `-1` returns the last code point, `-2` the last two, and so on. However, a negative position whose magnitude is equal to or greater than the text length returns empty rather than the whole text.

## Practical

Use this function to remove one or more initial characters, or select a suffix. [$restw()](restw.md) works in words, and [$mid()](mid.md) selects a substring with a specified start and length.

## Returns

The selected suffix, or empty for empty input or a position outside the accepted range. It returns a new string without modifying the input variable, produces no normal output, and requires no IRC connection.

## Restrictions

Use valid UTF-8 text. The preliminary quick code-point count and forward/backward decoding do not constitute a full UTF-8 validation step; malformed input can give inconsistent results. Positions are stored in a C `int`; oversized values and the most-negative integer are not safely range-checked.

## Examples

| Expression | Result |
| --- | --- |
| `$rest(4 foobarfnord)` | `arfnord` |
| `$rest(foobarfnord)` | `oobarfnord` |
| `$rest(1 5 testing)` | ` testing`, including the initial space. |
| `$rest(-2 hello)` | `lo` |
| `$rest(-5 hello)` | Empty string. |
| `$rest(123)` | `23` |

## Source

Registration and `function_rest()`: [source/functions.c](../../source/functions.c). Position detection: [source/ircaux.c](../../source/ircaux.c). Integer parsing: [include/functions.h](../../include/functions.h). Unicode counting and traversal: [source/wcwidth.c](../../source/wcwidth.c).
