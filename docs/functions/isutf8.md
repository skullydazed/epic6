# $isutf8()

## Synopsis

`$isutf8(<text>)`

## Technical

Examines the entire supplied byte string and returns a count based on UTF-8-style sequence structure. Input is not split into words or dequoted; spaces and literal quotation marks are included. Empty or omitted input returns the empty string.

For ordinary well-formed UTF-8, the result is the number of code points, including ASCII characters. This is not a Boolean validator or a count of screen columns or user-perceived characters.

The structural check is permissive: it accepts standalone continuation bytes, overlong encodings, surrogate encodings, values beyond Unicode's maximum, and obsolete five- or six-byte sequences if their continuation-byte structure fits. A positive result therefore does not establish that the text is valid modern UTF-8.

The function does not decode to Unicode values, change the input, trim an incomplete trailing character, convert encodings, or produce an error diagnostic. It uses a separate counting helper from EPIC's incoming-text validation.

## Returns

| Result | Meaning |
| --- | --- |
| Positive integer | Structural count: ASCII bytes plus sequence-leading bytes, including standalone continuation bytes treated as individual characters. |
| Negative integer | An expected continuation byte was missing or replaced by another kind of byte. The magnitude counts ASCII bytes and sequence starts seen so far, including the start of the failing sequence. |
| `0` | An incomplete trailing sequence, a `0xfe`/`0xff` lead byte, or another final length mismatch. |
| Empty string | Empty or omitted input. |

## Practical

Use the positive count for text already known to be well-formed UTF-8. Do not use a truth test as validation: negative error results are also nonzero, and some malformed encodings return positive counts. See [is8bit](is8bit.md) for a separate high-bit-byte test.

## Examples

| Expression | Result |
| --- | --- |
| `$isutf8(hello)` | `5` |
| `$isutf8(café)` | `4` for UTF-8 text |
| `$isutf8(a b)` | `3` |
| `$isutf8("hi")` | `4` (quotation marks are literal) |
| `$isutf8()` | Empty string |

For raw bytes, `c3 41` returns `-1`, a lone `c3` returns `0`, and a lone continuation byte `80` returns `1`.

## Source

Registered and implemented as `function_isutf8` in [source/functions.c](../../source/functions.c). Its structural counter is `num_code_points` in [source/ircaux.c](../../source/ircaux.c).
