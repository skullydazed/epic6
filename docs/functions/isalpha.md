# isalpha function

## Synopsis

`$isalpha(<text>)`

## Technical

Tests whether the first byte of the supplied text is an ASCII letter: `a` through `z` or `A` through `Z`. Only that byte is examined; the rest of the string is ignored. Unlike a locale-aware C character classifier, this built-in uses explicit ASCII ranges and is unaffected by locale.

Input is not split into argument words or dequoted. A leading space, quotation mark, or other nonletter therefore returns false. Empty or omitted input also returns false. Non-ASCII letters encoded as UTF-8 are not recognized; this is not a Unicode alphabetic test.

## Practical

Use this to test whether text begins with an ASCII letter. Like [$isdigit()](isdigit.md), it does not validate the entire string. It is not a reliable way to decide whether text is a number, hostname, or IP address; use the appropriate conversion or validation function for that purpose.

## Returns

`1` if the first byte is an ASCII letter, otherwise `0`. There is no empty error result or diagnostic.

## Examples

| Expression | Result |
| --- | --- |
| `$isalpha(hello)` | `1` |
| `$isalpha(A123)` | `1` |
| `$isalpha(2hello)` | `0` |
| `$isalpha("hello")` | `0` (the quotation mark is literal) |
| `$isalpha(éclair)` | `0` for UTF-8 text |
| `$isalpha()` | `0` |

## Source

Registration and direct first-byte ASCII-range comparisons: [source/functions.c](../../source/functions.c). Integer-string return formatting: [include/functions.h](../../include/functions.h).
