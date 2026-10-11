# ascii function

## Synopsis

```text
$ascii(character-list)
```

## Technical

`ascii()` converts the entire argument string into a space-separated list of decimal byte values, one value for each byte. Each value is between 1 and 255; the terminating NUL byte is not included. Empty input returns the empty string.

Despite its name, this function is not restricted to ASCII characters, and it does not decode Unicode code points. A UTF-8 character occupying several bytes produces several numbers. Invalid UTF-8 bytes are included unchanged rather than rejected or skipped. High-bit bytes produce positive values, never negative ones.

The function does not parse words or remove quotes: spaces, quotes, and other characters present in its argument are included in the conversion. Normal script expansion occurs before the function receives that string.

[`chr()`](chr.md) converts decimal byte values back into a string, so `$chr($ascii(text))` reconstructs the supplied non-NUL bytes. For Unicode code-point descriptions such as `U+00E9`, use [`unicode()`](unicode.md) instead. [`asciiq()`](asciiq.md) applies CTCP decoding before byte conversion.

## Examples

| Expression | Return value |
| --- | --- |
| `$ascii(abcABC)` | `97 98 99 65 66 67` |
| `$chr($ascii(abcABC))` | `abcABC` |
| `$ascii(a b)` | `97 32 98` |
| `$ascii(é)` | `195 169` when the argument contains UTF-8 encoded `é` |
| `$ascii()` | Empty string |

## Other notes

The result reflects the bytes actually supplied to the function, so a different encoding of the same visible character produces different values. It performs no encoding conversion and cannot inspect bytes after an embedded NUL, because EPIC passes its input as a NUL-terminated string.

## Source

[source/functions.c](../../source/functions.c) registers `ascii` and implements `function_ascii()`, the byte reconstruction in `function_chr()`, and the distinct code-point conversion in `function_unicode()`.
