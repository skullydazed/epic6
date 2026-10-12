# $leftpc()

## Synopsis

`$leftpc(<count> <text>)`

## Technical

Returns the first prepared display line of the text, using `<count>` as its column-width limit and accounting for ANSI and IRC formatting. It normalizes the text into internal attributes, prepares one line without ordinary word wrapping, then converts attributes back to IRC formatting codes. It does not display the text or preserve the original escape-byte spelling.

The count is required and uses base-detecting integer conversion, including hexadecimal and leading-zero octal. Nonnumeric input converts to zero. A negative count or missing text returns the empty string. Zero is passed to the display-line preparer rather than rejected.

Everything after the count is text, including spaces and literal quotation marks; it is not separately dequoted. Printable Unicode characters are measured in EPIC's display columns, rather than bytes or code points. Formatting changes consume no columns. Newlines can end the first prepared line. Normalization drops unsupported or unprintable controls and skips malformed UTF-8 bytes.

## Returns and restrictions

Returns the first prepared line in logical IRC formatting. This is a display-pipeline operation, not a guaranteed exact-width Unicode substring. Use [left](left.md) for code-point extraction.

The preparer prepends [first_line](../settings/first_line.md), so that setting can add text to the result. [indent](../settings/indent.md) and [continued_line](../settings/continued_line.md) participate in its inherited line-break logic and can affect the boundary. It adds no padding to fill the requested width.

The current line-break code uses a byte boundary when truncating, so multibyte characters at the boundary can be cut incorrectly. Its near-end shortcut and continuation-marker adjustments can also retain more than the requested width. Do not rely on a strict width limit or intact UTF-8 at every boundary. Returned formatting can include attribute changes or resets beyond the printable text.

## Examples

```text
$leftpc(10 onetwothre$chr(27)[34mefour)
```

With `first_line` empty and ordinary continuation settings, the printable prefix is `onetwothree`; the ANSI blue change becomes IRC formatting in the returned string. Formatting bytes do not count toward the requested width.

For a plain short string, `$leftpc(10 abc)` returns printable `abc` without padding, subject to any configured first-line prefix.

## Source

Registered and implemented as `function_leftpc` in [source/functions.c](../../source/functions.c). Normalization, display-column counting, line preparation, and denormalization are in [source/screen.c](../../source/screen.c). Unicode decoding and width calculation are in [source/wcwidth.c](../../source/wcwidth.c).
