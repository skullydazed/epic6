# $nohighlight()

## Synopsis

`$nohighlight(<string>)`

## Technical

Returns the string with selected formatting control bytes rendered as visible, reverse-video letters. Each selected byte is replaced by a reverse toggle (`0x16`), the control byte ORed with `0x40`, and another reverse toggle. It escapes those bytes rather than removing them or producing plain text.

| Input control | Hex byte | Visible letter inserted |
| --- | --- | --- |
| Bold (`^B`) | `02` | `B` |
| Color (`^C`) | `03` | `C` |
| Alternate character set (`^E`) | `05` | `E` |
| Blink (`^F`) | `06` | `F` |
| Reset (`^O`) | `0F` | `O` |
| Reverse (`^V`) | `16` | `V` |
| Extended color (`^X`) | `18` | `X` |
| Escape (`^[`) | `1B` | `[` |
| Underline (`^_`) | `1F` | `_` |

The entire input is processed literally, including spaces and quotation marks; it is not parsed into words or dequoted. Color numbers and text following an escape byte are retained. For example, a color byte followed by `04` becomes a reverse-video `C` followed by the ordinary digits `04`.

## Practical

This compatibility function can expose some formatting bytes when inspecting text. For configurable removal of formatting, use [$stripcrap()](stripcrap.md) instead.

It does not guarantee unformatted output: its own markers use reverse toggles, and the italic toggle (`^P`, `0x10`) is not escaped. Other control bytes not listed above are also retained. It does not parse complete ANSI escape sequences.

## Returns

A copy of the transformed string. Empty input returns the empty string, and the source variable is not changed. Processing is byte based: ordinary UTF-8 bytes, including malformed sequences, are copied unchanged rather than validated or normalized.

## Examples

If the input contains actual bold and underline control bytes around the words in `this is a test`, those bytes become reverse-video `B` and `_` letters around the words. This differs from inserting literal caret notation such as `^B`.

`$nohighlight(plain text)` returns `plain text`. Literal characters `^B` remain literal characters `^B`; only the actual control byte triggers conversion.

Registration and `function_nohighlight()`: [source/functions.c](../../source/functions.c). Control byte definitions: [include/irc.h](../../include/irc.h).
