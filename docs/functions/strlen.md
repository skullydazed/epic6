# strlen

## Synopsis

`$strlen(<string>)`

## Technical

Determines the length of the entire argument in Unicode code points for valid UTF-8 text. Spaces, literal quotation marks, and formatting/control characters are included. There is no word parsing or special JSON form.

The count is not a byte length, screen width, or count of displayed graphemes: a combining mark counts separately, and a wide character counts as one. Use [`printlen`](printlen.md) to measure display columns.

## Practical

Use this function to measure literal text, function results, or automatic expandos. For ASCII text, its count agrees with `$@var`; for multibyte UTF-8 text, `$@var` counts bytes and can differ from `strlen`.

## Returns

A decimal integer: the number of code points, or `0` for empty input. There are no options, settings, or server requirements.

The implementation performs a quick count without validating UTF-8: every byte below `0x80` or at least `0xC0` counts once, and bytes `0x80` through `0xBF` do not count. Malformed text therefore need not produce a meaningful character count. Use [`isutf8`](isutf8.md) when validation is required.

## Examples

```irc
$strlen($1-3)            Returns the length of arguments $1 through $3, including spaces.
$strlen(epic is cool)    Returns 12.
$strlen(café)            Returns 4 for UTF-8 text.
$strlen(界)              Returns 1, regardless of its display width.
$strlen()                Returns 0.
```

## Source

Handler: [`source/functions.c`](../../source/functions.c:4849). Counting: [`quick_code_point_count`](../../source/wcwidth.c:555). The `$@` byte count is implemented in [`source/expr.c`](../../source/expr.c:518).
