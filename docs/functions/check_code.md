# check_code function

## Synopsis

```text
$check_code(text)
```

## Technical

`check_code()` performs a limited structural check on its entire argument string. After skipping leading whitespace, it requires one brace block (`{...}`) or parenthesized expression (`(...)`), a matching closing delimiter, and only whitespace after that delimiter. It does not execute the block or evaluate the expression.

The matching helper counts nested instances of the chosen delimiter pair. A backslash followed by another character skips that next character during matching. Double quotes do not protect delimiters from this counting, and other delimiter types are not checked: a brace block is checked for braces, not for balanced parentheses or square brackets inside it.

## Returns

| Result | Meaning |
| --- | --- |
| `0` | A matching outer block/expression was found with no trailing non-whitespace text |
| `-1` | Missing/empty input, or the first non-whitespace character is neither `{` nor `(` |
| `-2` | No matching closing brace or parenthesis was found |
| `-3` | Non-whitespace text follows the matching closing delimiter |

The success value is `0`, not `1`. Errors are returned numerically without the delimiter helper's normal syntax diagnostics.

## Practical and restrictions

Use this to check whether text has the outer structure expected by a command before passing it onward. Success does not establish that command names, expressions, arguments, quoting, or nested constructs are valid. It is not a parser for the complete EPIC language or a safety check for executing untrusted code.

Empty `{}` and `()` pass this structural check. Two consecutive blocks do not: the second is trailing text. Normal script expansion occurs before the function receives its argument, so the checked text can differ from the original source expression.

## Examples

| Expression | Return value |
| --- | --- |
| `$check_code({echo hello})` | `0` |
| `$check_code({})` | `0` |
| `$check_code((1 + 2))` | `0` |
| `$check_code(echo hello)` | `-1` |
| `$check_code({echo hello} extra)` | `-3` |
| `$check_code()` | `-1` |

For text supplied through a variable, an unmatched outer opener such as `{echo hello` returns `-2`. Supplying malformed delimiters directly in a function call can instead interfere with the surrounding script parser before this function is invoked.

## Source

[source/functions.c](../../source/functions.c) registers `check_code` and implements `function_check_code()`. [source/if.c](../../source/if.c) implements `next_expr_failok()` and block extraction; [source/ircaux.c](../../source/ircaux.c) implements `MatchingBracket()`.
