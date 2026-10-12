# is8bit function

## Synopsis

`$is8bit(<text>)`

## Technical

Returns the zero-based byte position of the first byte in `text` with its high bit set, meaning a byte value from 128 through 255. It examines the entire input literally; spaces and quotation marks are part of the text, rather than argument separators or grouping syntax. `/xdebug dword` does not change this parsing.

This is a byte test, not a Unicode character test or encoding validator. Valid UTF-8 containing a non-ASCII character has high-bit bytes, but invalid UTF-8 and text in other encodings can have them too. Positions count bytes, unlike the Unicode code-point positions returned by [$index()](index.md).

## Practical

Use this to locate the first non-ASCII byte in a string. It does not establish whether the string is UTF-8; see [$isutf8()](isutf8.md) for that function's validation behavior. ASCII control bytes have no high bit and therefore do not count as matches.

## Returns

A nonnegative byte position when a high-bit byte exists. Returns `-1` for a nonempty string containing only bytes below 128, or empty for missing/empty input. There is no diagnostic.

This is not a Boolean result: position zero is a successful match, and `-1` is failure. Compare against zero rather than testing numeric truth alone.

## Examples

| Expression | Result |
| --- | --- |
| `$is8bit(hello there)` | `-1` |
| `$is8bit(éclair)` | `0` |
| `$is8bit(café)` | `3` |
| `$is8bit("é")` | `1` (the leading quotation mark is literal) |
| `$is8bit()` | Empty string |

The non-ASCII examples assume the text is encoded as UTF-8.

## Source

Registration, literal input scan, high-bit test, and return values: [source/functions.c](../../source/functions.c). Integer-string and empty-result macros: [include/functions.h](../../include/functions.h).
