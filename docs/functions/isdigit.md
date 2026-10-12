# isdigit function

## Synopsis

`$isdigit(<text>)`

## Technical

Tests whether the supplied text starts with an ASCII digit (`0` through `9`), or with a minus sign immediately followed by an ASCII digit. Only the first byte and, after a minus sign, the second byte are examined. The remaining text is ignored.

Input is not split into words or dequoted. Leading spaces and quotation marks therefore affect the result. A leading plus sign is not accepted. Empty or omitted input returns `0`.

The built-in uses explicit ASCII ranges, independent of locale. Non-ASCII digits encoded as UTF-8 are not recognized; this is not a Unicode digit test.

## Practical

Use this to check whether text begins with a digit or a minus sign followed by a digit. Like [isalpha](isalpha.md), it does not validate the entire string: `2abc` and `-2abc` both return `1`. It is not a reliable test for whether text is a complete number, hostname, or IP address.

## Returns

`1` if the prefix qualifies, otherwise `0`. There is no empty error result or diagnostic.

## Examples

| Expression | Result |
| --- | --- |
| `$isdigit(123)` | `1` |
| `$isdigit(2abc)` | `1` |
| `$isdigit(-2abc)` | `1` |
| `$isdigit(a123)` | `0` |
| `$isdigit(+123)` | `0` |
| `$isdigit(-)` | `0` |
| `$isdigit("123")` | `0` (the quotation mark is literal) |
| `$isdigit(١٢٣)` | `0` for UTF-8 text |
| `$isdigit()` | `0` |

## Source

Registration and direct prefix comparisons are in [source/functions.c](../../source/functions.c). Integer-string return formatting is in [include/functions.h](../../include/functions.h).
