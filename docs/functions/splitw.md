# $splitw()

## Synopsis

`$splitw(<delimiter> <text>)`

## Technical

Splits text at occurrences of one Unicode delimiter codepoint and returns the nonempty fields as a space-separated word list. Only the first decodable codepoint of the delimiter argument is used; additional characters in that argument are ignored. Matching is exact and case-sensitive. There is no character-set inversion, wildcard syntax, or delimiter escaping.

The delimiter is a double-quoted word, allowing a space delimiter to be supplied as `" "` regardless of `/xdebug dword`. The complete remaining text is split. Quotes and backslashes in that text do not protect delimiters, and fields are not dequoted.

Output quoting is enabled independently of [/xdebug dword](../commands/xdebug.md): a nonempty field containing a literal space is enclosed in double quotes, with embedded double quotes escaped. Fields are joined by single spaces. A field containing only tabs or other whitespace is not automatically quoted merely for that reason, and fields containing quotes but no spaces are copied without additional quoting. This is not a general encoding for every possible word value.

Leading, adjacent, and trailing delimiters yield empty fields that are omitted from the result. Other spaces inside each field are preserved. An empty delimiter argument selects the zero codepoint and finds no delimiter in ordinary text, so the entire nonempty text becomes one output field.

## Practical

Use this to turn a delimited string such as a search path into a list while retaining fields containing spaces. [$split()](split.md) replaces characters from a set with spaces and does not quote fields. To parse grouped fields from the returned list with ordinary word functions, enable DWORD or use functions that explicitly honor double quotes.

## Returns

The rebuilt field list, or empty when input is missing, text is empty, or every field is empty. It returns a new string without assigning a source variable. No IRC connection is required.

## Restrictions

Use valid UTF-8. The delimiter parser skips invalid leading bytes, and the splitter resynchronizes over malformed text while retaining slices of the original bytes; malformed data is not validated or normalized. There are no options or separate ordinary error codes.

## Examples

| Expression | Result |
| --- | --- |
| `$splitw(: /bin:/usr/bin:/home/user/my programs/)` | `/bin /usr/bin "/home/user/my programs/"` |
| `$splitw(: :one::two:)` | `one two` |
| `$splitw(: ab:cd)` | `ab cd` |
| `$splitw(:; a;b:c)` | `a;b c` (only `:` is the delimiter) |
| `$splitw(é aébé)` | `a b` |
| `$splitw("" one two)` | `"one two"` |

Registration and `function_splitw()`: [source/functions.c](../../source/functions.c). `new_split_string()`, `unsplitw()`, and output quoting: [source/ircaux.c](../../source/ircaux.c). UTF-8 decoding: [source/wcwidth.c](../../source/wcwidth.c).
