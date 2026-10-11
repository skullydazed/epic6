# $cp437test()

## Synopsis

`$cp437test()`

## Technical

Generates a fixed test string for EPIC's built-in CP437-to-UTF-8 conversion, displays it through normal client output, and returns the same string. All supplied arguments are ignored; this is a diagnostic function, not a converter for user-provided text.

The string contains the mapped characters for CP437 byte values 1 through 255 in ascending order. The generated NUL bytes contribute no characters. Values 1 through 31 remain their corresponding control characters rather than the pictorial glyphs associated with some DOS displays. Printable ASCII stays unchanged, byte 127 becomes U+2302 (⌂), and the upper half uses EPIC's CP437 mapping, including accented letters, box drawing, and symbols.

## Practical

Use this function to inspect how EPIC's converted CP437 characters appear on your terminal. Its display includes normal client banner formatting and respects output suppression. The returned string has no added banner.

## Returns

Returns the generated UTF-8 test string, including its control characters. Calling the function also attempts to display that string before returning.

## Restrictions

The conversion uses a built-in mapping table and does not require the system to provide a CP437 encoding converter. It is always registered. Terminal encoding, fonts, and EPIC's handling of control and formatting characters affect what you see; the output is not a plain chart with one visible glyph per byte.

## Examples

```irc
@ sample = cp437test()
```

This displays the test string and stores the unprefixed return value in `sample`. An `echo` of `$cp437test()` would add a second display of the return value.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). The CP437 mapping and conversion helper are in [source/ircaux.c](../../source/ircaux.c); normal banner output and suppression are handled in [source/output.c](../../source/output.c).
