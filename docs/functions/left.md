# $left()

## Synopsis

```text
$left(<count> <text>)
$left({"number":<count>,"text":"<text>"})
```

## Technical

Returns a copy of the first `<count>` Unicode code points of the supplied text. Missing count or text, or a count less than or equal to zero, returns the empty string. If the text is no longer than the count, the complete text is returned. No padding is added.

In the ordinary form, the count is an integer function argument with base-detecting conversion, including hexadecimal and leading-zero octal. Nonnumeric text converts to zero. Everything after the count is the text, including additional words and literal quotation marks; it is not separately dequoted.

When input starts with `{`, the JSON keyword form supplies `number` and `text`. Missing fields produce zero/empty values. Malformed JSON prints a parsing diagnostic and returns the empty string. This diagnostic does not update [json_error](json_error.md).

Counts are code points, not bytes, screen columns, or complete user-perceived characters. Combining sequences can be split, and control/formatting characters count too. Malformed UTF-8 does not have reliable character-count semantics: the truncation loop advances one byte for an invalid sequence, while the whole-string shortcut uses a different quick count. The function does not validate or repair the text.

## Practical

Use this to extract the beginning of a string. To take text before a delimiter, [before](before.md) avoids the older `$left($index(<char> <text>) <text>)` construction and the need to supply the same text twice. [index](index.md) remains useful for locating a character-set match.

For padding to a display width, see [pad](pad.md); display columns and code-point counts can differ, so padding does not generally make the result exactly `<count>` code points long. [leftpc](leftpc.md) is the related display-oriented extraction function.

## Returns

The initial text, without changing the source variable, or the empty string for missing input or a nonpositive count. See [right](right.md) for extraction from the end.

## Examples

| Expression | Result |
| --- | --- |
| `$left(5 biklmnopstv)` | `biklm` |
| `$left(15 biklmnopstv)` | `biklmnopstv` |
| `$left(-2 biklmnopstv)` | Empty string |
| `$left(2 café)` | `ca` |
| `$left(1 élan)` | `é` for UTF-8 text |
| `$left(2 "abc")` | `"a` (quotation mark is literal) |
| `$left({"number":2,"text":"abcd"})` | `ab` |

## History

This function first appeared in ircII.

## Source

Registered and implemented as `function_left` in [source/functions.c](../../source/functions.c), including JSON keyword parsing. Integer argument parsing is in [include/functions.h](../../include/functions.h), quick counting is in [source/ircaux.c](../../source/ircaux.c), and code-point traversal is in [source/wcwidth.c](../../source/wcwidth.c).
