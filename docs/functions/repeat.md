# repeat function

## Synopsis

`$repeat(<count> <text>)`

## Technical

Returns `count` copies of the text joined without a separator. The count is parsed as a decimal integer prefix, and exactly one following character is consumed as the separator if present. Everything after that is repeated literally, including additional spaces and quote characters. The separator is not checked to be whitespace; use the documented space-separated form rather than relying on that permissiveness.

The text is not a quoted word or a word list. Do not add surrounding double quotes unless you want the quotes repeated too. To repeat one space, put two spaces after the count: one is consumed as the separator and the other is the text. There is no JSON argument form.

## Returns

The repeated text. A parsed count less than one returns the empty string. Missing or empty text also yields empty. Ordinary EPIC argument expansion occurs before the handler receives the text; this function constructs a string and does not execute it. The [/repeat command](../commands/repeat.md) instead executes commands repeatedly.

## Restrictions

There is no current 1 MB truncation limit. The implementation allocates `byte length of text × count + 1` bytes for the result, without checking multiplication overflow, then appends the text repeatedly. Large requests can exhaust memory or overflow allocation arithmetic; repeated concatenation can also be expensive.

The decimal parser returns an unsigned long which is assigned to a C `int`. Supply a nonnegative decimal count within the positive `int` range; negative or oversized input can undergo unsigned conversion and narrowing rather than clean range validation. Missing or nonnumeric input normally parses as zero and returns empty. The function repeats bytes without encoding conversion and requires no IRC connection.

## Examples

| Expression | Result |
| --- | --- |
| `$repeat(5 hello)` | `hellohellohellohellohello` |
| `$repeat(5  )` | Five spaces. |
| `$repeat(2 "hi")` | `"hi""hi"` |
| `$repeat(0 hello)` | Empty string. |

## Source

Registration and `function_repeat()`: [source/functions.c](../../source/functions.c). Allocation and string helpers: [source/ircaux.c](../../source/ircaux.c).
