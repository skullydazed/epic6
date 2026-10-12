# $mid()

## Synopsis

```text
$mid(<start> <length> <text>)
$mid({"start":<start>,"number":<length>,"text":"<text>"})
```

## Technical

Skips the first `start` Unicode code points and returns up to the next `length` code points. The starting position counts from zero: `0` selects the beginning. If fewer code points remain than requested, the entire remainder is returned without padding.

Missing start, length, or text returns the empty string. A negative start, a nonpositive length, or a start at or beyond the end also returns the empty string. A negative length does not mean “the rest of the string.”

In the ordinary form, start and length are integer function arguments with base-detecting conversion, including hexadecimal and leading-zero octal. Nonnumeric arguments convert to zero. Everything remaining after those two arguments is text, including spaces, additional words, and literal quotation marks; it is not separately dequoted.

When input starts with `{`, the JSON keyword form supplies `start`, `number`, and `text`. Missing fields produce zero/empty values: omitting `start` means position zero, while omitting `number` or `text` produces an empty result. Malformed JSON prints a parsing diagnostic and returns the empty string; it does not update [$json_error()](json_error.md).

Counts are code points, not bytes, screen columns, or complete user-perceived characters. Combining sequences can be split, and control/formatting characters count too. Malformed UTF-8 has unreliable counting semantics: traversal advances one byte for an invalid sequence, but length shortcuts use a different quick counter. The function does not validate or repair text.

## Practical

Use this function to extract a substring or a field from a string with fixed code-point positions. Unlike ircII, EPIC does not accept a negative length to select the remainder; use [$rest()](rest.md) for that. [$left()](left.md) and [$right()](right.md) select text from either end.

## Returns

A copy of the selected portion of text, or the empty string under the conditions above. The source variable is not changed.

## Examples

| Expression | Result |
| --- | --- |
| `$mid(4 4 hello there)` | `o th` |
| `$mid(7 100 this is a long string)` | ` a long string` |
| `$mid(7 -1 this is a long string)` | Empty string |
| `$mid(-1 2 hello)` | Empty string |
| `$mid(1 2 café)` | `af` |
| `$mid(3 1 café)` | `é` for UTF-8 text |
| `$mid(0 2 "abc")` | `"a` (quotation mark is literal) |
| `$mid({"start":1,"number":2,"text":"abcd"})` | `bc` |

## History

This function first appeared in ircII.

Registration, `function_mid()`, and JSON keyword parsing: [source/functions.c](../../source/functions.c). Integer argument parsing: [include/functions.h](../../include/functions.h). Quick counting and code-point traversal: [source/wcwidth.c](../../source/wcwidth.c).
