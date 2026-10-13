# $shiftbrace()

## Synopsis

`$shiftbrace(<variable name>)`

## Technical

If the named variable's value begins immediately with a braced expression, removes that expression and returns its contents without the outer braces. Writes the remaining text back to the variable. It does not evaluate or execute the extracted contents.

Only the first function word supplies the variable name; additional arguments are ignored. There is no literal-text form. Names use normal lookup and assignment scope: visible locals are updated, otherwise assignment is global; `:name` and `::name` explicitly select local or global writeback. See [/local](../commands/local.md) and [/assign](../commands/assign.md).

Nested braces are balanced. A backslash escapes the following character for brace matching, but is retained in the extracted text. Quotation marks do not shield braces: an unescaped brace inside quotes still participates in nesting.

Whitespace matters. Leading spaces before the opening brace prevent extraction. Inside the braces, leading whitespace and ordinary trailing whitespace are trimmed; backslash-protected trailing whitespace can remain. Whitespace immediately after the closing brace is skipped before the remainder is assigned. Other internal and remaining-text spacing is preserved.

## Returns

The extracted contents, or empty when the variable name is missing/empty, the value does not begin with `{`, or no matching closing brace exists. Failed extraction leaves the variable unchanged. A nonbraced or empty value prints `Expression syntax`; an unmatched opening brace prints an unmatched-brace diagnostic.

An empty braced expression is a successful extraction returning empty and changing the variable. If no text remains, global writeback removes the assigned value, while a local variable remains defined with an empty value. Invalid assignment names can produce a diagnostic. No IRC connection is required.

## Practical

Use this to consume a variable containing successive braced groups. [$shift()](shift.md) removes a first word instead, and [$check_code()](check_code.md) checks delimiter balance without extracting from a variable.

## Examples

```irc
assign qwer {asdf zxcv} {zxcv asdf qwer} one two
```

Successive calls to `$shiftbrace(qwer)` return `asdf zxcv`, then `zxcv asdf qwer`. A third call returns empty with an expression-syntax diagnostic and leaves `qwer` as `one two`.

A value of `{outer {inner}} tail` returns `outer {inner}` and leaves `tail`. A value beginning with a space before `{` is not extracted.

Registration and `function_shiftbrace()`: [source/functions.c](../../source/functions.c). `next_expr()` extraction: [source/if.c](../../source/if.c). Brace balancing and whitespace handling: [source/ircaux.c](../../source/ircaux.c). Variable lookup and assignment: [source/alias.c](../../source/alias.c).
