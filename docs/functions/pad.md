# $pad()

## Synopsis

`$pad(<width> <char> [<text>])`

## Technical

Pads text to a minimum **display width**, without truncating longer text. A positive width appends padding on the right; a negative width prepends padding on the left. Zero width adds no padding. The required number of repetitions is the absolute width minus the text's measured display columns, when that difference is positive.

The width is an integer function argument with base-detecting conversion, including hexadecimal and leading-zero octal. Nonnumeric text converts to zero. It is stored as a C `int`; keep the value and its absolute value representable in that type.

The padding argument always recognizes double-quoted words, independently of [/xdebug dword](../commands/xdebug.md). Only its first decoded Unicode code point is used. A malformed initial sequence, an empty padding argument, or a character that does not occupy exactly one display column falls back to an ordinary space. This includes wide characters, combining marks, and control characters.

Everything after the padding argument is text, including additional words and literal quotation marks; that text is not separately dequoted. Omitted text is treated as empty and padded to the requested width.

## Practical

Use this to align strings in columns, for example when formatting a [/names](../commands/names.md) reply. Width is measured in terminal columns, not bytes or the code-point count returned by [$strlen()](strlen.md). [$fix_width()](fix_width.md) additionally supports truncation and centering.

## Returns

The padded text, without changing the source variable or displaying it. Missing width or padding argument returns the empty string. An explicitly empty quoted padding argument is accepted and uses spaces.

Text is normalized using the display-line mangling settings, measured, then converted back to IRC formatting. Even when it needs no padding, its formatting bytes can change; it is not guaranteed to return an identical byte sequence. Wide characters contribute their display width, while formatting and combining marks do not contribute ordinary printable columns. The selected mangling settings can strip or alter text before measurement.

## Examples

For ordinary text and display settings that retain it:

| Expression | Result |
| --- | --- |
| `$pad(8 ! test)` | `test!!!!` |
| `$pad(-8 ! test)` | `!!!!test` |
| `$pad(2 ! test)` | `test` (not truncated) |
| `$pad(0 ! test)` | `test` |
| `$pad(3 !)` | `!!!` |
| `$pad(4 ! 界)` | `界!!` when `界` occupies two columns |

`$pad(8 " " test)` appends four spaces. `$pad(8 "" test)` also uses spaces.

## History

This function first appeared in EPIC4pre1.046.

Registration and `function_pad()`: [source/functions.c](../../source/functions.c). Display-width padding and normalization: [source/ircaux.c](../../source/ircaux.c), `fix_string_width()`, with display helpers in [source/screen.c](../../source/screen.c). Code-point decoding and column widths: [source/wcwidth.c](../../source/wcwidth.c).
