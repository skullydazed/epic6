# reverse function

## Synopsis

`$reverse(<text>)`

## Technical

Returns a copy of the text with its Unicode code points in reverse order. For valid UTF-8, the bytes within each code point retain their order: the last code point becomes first, the next-to-last becomes second, and so on. This is character reversal, not word reversal; [$revw()](revw.md) reverses a word list instead.

The entire argument string is text. Spaces, literal quotes, and control characters participate in reversal; there is no word splitting, quote removal, formatting normalization, or encoding conversion. Empty input returns the empty string. The input variable is not modified.

## Practical

Use this function to reverse a string's code-point order, including spaces between words.

## Returns

The reversed text, with no normal screen output or special numeric error return. No IRC connection is required.

## Restrictions

Code-point reversal is not grapheme reversal: combining marks and components of emoji sequences are reversed separately from the characters they accompany. Formatting controls are moved as text rather than adjusted to preserve their visual effect.

Supply valid UTF-8. Backward traversal searches for decodable code points, so malformed byte sequences need not be preserved or reversed independently and can be grouped with neighboring bytes or omitted. The function is not a UTF-8 validator. The allocation size is stored in a C `int`, so extremely large strings are not safely range-checked.

## Examples

| Expression | Result |
| --- | --- |
| `$reverse(welcome to our channel)` | `lennahc ruo ot emoclew` |
| `$reverse(café)` | `éfac` |
| `$reverse("ab")` | `"ba"`, including the reversed literal quote characters. |
| `$reverse()` | Empty string. |

## History

First appeared in +6, after ircII and before EPIC.

## Source

Registration and `function_reverse()`: [source/functions.c](../../source/functions.c). Backward code-point traversal through `previous_code_point2()`: [source/wcwidth.c](../../source/wcwidth.c).
