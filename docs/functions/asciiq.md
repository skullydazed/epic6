# asciiq function

## Synopsis

```text
$asciiq(ctcp-encoded-character-list)
```

## Technical

`asciiq()` CTCP-decodes its entire argument string, then converts the decoded bytes before the first NUL into a space-separated list of unsigned decimal values. It returns the empty string for missing or empty input, or when the decoded string begins with NUL.

Like [`ascii()`](ascii.md), it reports bytes, not Unicode code points. A multibyte UTF-8 character produces several values, each no greater than 255. High-bit bytes produce positive numbers. No UTF-8 validation or encoding conversion occurs.

The CTCP decoder recognizes these backslash sequences in the string actually received by the function:

| Encoded sequence | Decoded byte | Decimal value |
| --- | --- | --- |
| `\a` | CTCP delimiter | `1` |
| `\n` | Newline | `10` |
| `\r` | Carriage return | `13` |
| `\\` | Backslash | `92` |
| `\0` | NUL | Ends the returned list |

An unrecognized escape drops the backslash and keeps the following character; for example, `\q` becomes `q`. A final unpaired backslash decodes to NUL. Ordinary characters, spaces, and quotes are otherwise retained. Script quoting and expansion can act before this decoding, so [`chrq()`](chrq.md) is a convenient way to generate encoded input without manually escaping it.

## Restrictions

`chrq()` converts a byte list into a CTCP-encoded string. The two operations can reconstruct a byte list containing no NUL, but they are not a general binary inverse: `asciiq()` stops at the first decoded NUL and omits that byte and everything after it. Thus `$asciiq($chrq(65 0 66))` returns `65`, not `65 0 66`. For unquoted text, use `ascii()`.

CTCP encoding can transport NUL and other special bytes, but this function's conversion loop treats the decoded result as a NUL-terminated string. It does not inspect the transform's decoded length. Malformed escapes are handled as described above rather than rejected as syntax errors.

## Examples

| Expression | Return value |
| --- | --- |
| `$asciiq(hi!)` | `104 105 33` |
| `$asciiq($chrq(65 10 92))` | `65 10 92` |
| `$asciiq($chrq(65 0 66))` | `65` |
| `$asciiq($chrq(0 65))` | Empty string |
| `$asciiq()` | Empty string |

## History

The `asciiq()` function first appeared in EPIC4-1.1.8.

## Source

[source/functions.c](../../source/functions.c) registers `asciiq` and implements `function_asciiq()` and `function_chrq()`. [source/ircaux.c](../../source/ircaux.c) implements `transform_string_dyn()` and the CTCP encoder/decoder.
