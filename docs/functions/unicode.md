# unicode

## Synopsis

`$unicode(<text>)`

## Technical

Decodes the entire argument as UTF-8 and returns a space-separated list of code-point descriptions. Each description starts with uppercase `U+`, followed by uppercase hexadecimal digits padded to at least four places. Higher values use more digits, for example `U+1F600`.

The input is text, not a numeric list: `$unicode(U+0041)` describes the characters `U`, `+`, `0`, `0`, `4`, and `1`. Spaces, literal quotation marks, and control characters also receive descriptions. There is no word parsing, JSON form, or formatting removal.

## Practical

Use this function to inspect the code points in text, including invisible formatting controls. [`chr`](chr.md) accepts the returned `U+` descriptions to reconstruct ordinary valid text. [`ascii`](ascii.md) instead describes bytes in decimal.

## Returns and restrictions

A list of descriptions, or empty for empty input. Structurally undecodable bytes are skipped individually, without a diagnostic. An input consisting only of rejected bytes supplies no descriptions.

The decoder checks byte sequence shapes but does not reject overlong encodings, surrogate values, or decoded values above Unicode's scalar range. An overlong encoding that decodes as zero stops the scan. Consequently, the result is not proof that the input is valid Unicode, and malformed input need not round-trip through `chr`. No normalization, case conversion, or grapheme grouping is performed. There are no options, settings, or server requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$unicode(hi!)` | `U+0068 U+0069 U+0021` |
| `$unicode(A B)` | `U+0041 U+0020 U+0042` |
| `$unicode(café)` | `U+0063 U+0061 U+0066 U+00E9` for UTF-8 text |
| `$unicode(😀)` | `U+1F600` for UTF-8 text |
| `$unicode()` | Empty string |

## Source

Handler and formatting: [`source/functions.c`](../../source/functions.c:4182). Decoder: [`next_code_point2`](../../source/wcwidth.c:218). Reconstruction: [`function_chr`](../../source/functions.c:4017).
