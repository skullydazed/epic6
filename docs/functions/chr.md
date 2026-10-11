# chr function

## Synopsis

```text
$chr(byte-or-codepoint ...)
```

## Technical

`chr()` converts a word list into a sequence of bytes or UTF-8 characters. Ordinary numeric words represent byte values from 0 to 255. Words beginning with the exact uppercase prefix `U+` represent hexadecimal Unicode code points, which are converted to UTF-8. These two forms can be mixed in one call.

Ordinary numbers use `strtol()` with base detection, accepting decimal, hexadecimal (`0x`), and leading-zero octal notation. Numeric prefixes can be accepted with trailing nonnumeric text. Values below zero or above 255 are skipped; negative values are not converted modulo 256. A nonnumeric ordinary word converts to zero, with the NUL limitation below.

For the `U+` form, the hexadecimal prefix is parsed even if more nonnumeric text follows it. If no hexadecimal digit can be parsed, that word is skipped. Use valid Unicode scalar values (0–0x10FFFF, excluding surrogates): the conversion helper does not comprehensively reject invalid code points, and out-of-range input can produce malformed bytes.

Argument word parsing honors [`xdebug dword`](../commands/xdebug.md). The function returns a string rather than displaying it. Its bytes may include formatting or control characters, whose effects depend on how the caller subsequently uses the string.

## Encoding and restrictions

[`ascii()`](ascii.md) reports a string's bytes, so `$chr($ascii(text))` reconstructs the original non-NUL bytes. Numeric values greater than 127 are raw bytes, not automatically UTF-8 encodings of same-numbered Unicode characters. For example, `$chr(233)` produces one byte, while `$chr(U+00E9)` produces the two UTF-8 bytes for `é`. [`unicode()`](unicode.md) supplies `U+` descriptions for code-point conversion.

An ordinary numeric zero inserts NUL, which terminates the visible returned string; bytes appended after it are inaccessible through ordinary EPIC string operations. An invalid ordinary numeric word also becomes zero. By contrast, `U+0000` contributes no bytes because the Unicode branch copies its encoded output only up to NUL. Empty input returns the empty string.

Use [`chrq()`](chrq.md) when a CTCP-encoded representation is needed for special bytes. It is a distinct conversion, not merely another spelling of `chr()`.

## Examples

| Expression | Result |
| --- | --- |
| `$chr(3)` | Control-C, used as the color-change attribute |
| `$chr($ascii(abcABC))` | `abcABC` |
| `$chr(104 105 33)` | `hi!` |
| `$chr(U+00E9)` | UTF-8 `é` |
| `$chr(65 256 -1 66)` | `AB` |
| `$chr(65 0 66)` | Visible string `A` |
| `$chr(65 U+0000 66)` | `AB` |
| `$chr()` | Empty string |

## Source

[source/functions.c](../../source/functions.c) registers `chr` and implements `function_chr()`. [source/ircaux.c](../../source/ircaux.c) implements base-detected numeric conversion and `ucs_to_utf8()`. [include/words.h](../../include/words.h) defines function word parsing.
