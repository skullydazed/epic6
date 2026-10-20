# Math operators

EPIC's [expression mode](expressions.md) supports numeric, Boolean, string, comparison, and assignment operators. Use `@ expression` to evaluate an expression and discard its result, or `${expression}` to insert its result into expanded text.

## Tokens

An expression is composed of operators and operands. In `3 + 4`, the numbers are operands and `+` is the operator. A variable name can serve as an [lvalue](lval.md), meaning an assignment destination, or supply its current value when used as an rvalue. Numeric literals, string operands, function results, and completed operations supply values but are not automatically writable destinations.

Operands are converted as needed by each operator. The fact that a value was written as a quoted string does not prevent numeric comparison when its content is a valid number. Write ordinary variable names without `$` in expressions; use `[text]` or `"text"` for text with expansion, and `'text'` for literal text without expando expansion.

## Operators, precedence, and associativity

Lower precedence numbers bind more tightly. Operators at the same precedence associate in the indicated direction. Parentheses override grouping and may be nested. For example, `3 - 4 * 5 - 7` is `(3 - (4 * 5)) - 7`, giving `-24`.

| Precedence | Operators | Meaning | Associativity |
| --- | --- | --- | --- |
| 1 | `(expression)`, `function(arguments)` | Grouping and function calls | Left to right |
| 2 | `!`, `~`, prefix `++`/`--`, unary `+`/`-`, `@`, `#`, unary `*`, unary `**` | Logical/bitwise negation, increment/decrement, sign, byte length, word count, indirection, re-expansion | Right to left |
| 2 | suffix `++`/`--` | Increment/decrement, returning the previous value | Right to left |
| 3 | `**` | Exponentiation | Right to left |
| 4 | `*`, `/`, `%` | Multiplication, division, integer remainder | Left to right |
| 5 | `+`, `-`, `##` (also binary `#`) | Addition, subtraction, string concatenation | Left to right |
| 6 | `<<`, `>>` | Integer bit shifts | Left to right |
| 7 | `<`, `<=`, `>`, `>=`, `<=>` | Ordering comparisons; case-insensitive string comparison for `<=>` | Left to right |
| 8 | `=~`, `!~`, `in`, `not in` | Wildcard match/non-match; word-list membership/non-membership | Left to right |
| 9 | `==`, `!=`, `===`, `!==` | Equality/inequality; exact string equality/inequality for the three-character forms | Left to right |
| 10 | `&` | Bitwise and | Left to right |
| 11 | `^` | Bitwise xor | Left to right |
| 12 | `\|` | Bitwise or | Left to right |
| 13 | `&&` | Logical and | Left to right |
| 14 | `^^` | Logical xor | Left to right |
| 15 | `\|\|`, `??` | Logical or; choose the left value if true, otherwise the right value | Left to right |
| 16 | `condition ? yes : no` | Choose one of two values | Right to left |
| 17 | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `**=` | Assignment and arithmetic assignment | Right to left |
| 17 | `&=`, `^=`, `\|=`, `<<=`, `>>=`, `&&=`, `\|\|=`, `^^=` | Bitwise, shift, and logical assignment | Right to left |
| 17 | `#=` (also `##=`), `#~` | Append/prepend a string to a variable | Right to left |
| 18 | `,` | Evaluate both operands and return the right value | Left to right |


## Numeric and Boolean behavior

Arithmetic uses floating-point conversion, except integer operations such as `%`, bitwise operations, shifts, and increment/decrement. Division is not integer division just because the arguments look like integers. `%` converts both operands to integers before computing the remainder. Integer conversion uses the C base-zero convention, so leading `0` and `0x` can affect the interpretation of integer-operation operands.

Division or integer remainder by zero produces a diagnostic and an empty operand. Exponentiation uses the platform's `powl()`; invalid domains, overflow, and non-finite results depend on the numeric library. Avoid out-of-range integer conversions, integer overflow, and invalid shift counts; the parser is not an arbitrary-precision arithmetic system.

An empty string or a string consisting wholly of numeric zero is false. Other strings are true, including `off` and text following a leading zero. Logical operators return `0` or `1`; bitwise operators instead operate on integer bits.

Floating-point results are formatted to text using fixed-decimal conversion. With [floating_point_math](../set/floating_point_math.md) enabled, redundant trailing fractional zeros are removed; with it disabled, the fractional portion is discarded during text conversion. `floating_point_precision` does not control this parser's formatting.

## Comparison and strings

`==`, `!=`, `<`, `<=`, `>`, and `>=` compare numerically when both operands are recognized as real numbers; otherwise they use case-insensitive string comparison. `===` and `!==` always compare exact case-sensitive strings. Thus `[01] == [1]` is true, but `[01] === [1]` is false.

`<=>` is case-insensitive string comparison, returning a negative value, zero, or a positive value. It is not a swap operator. `=~` and `!~` treat the right operand as a wildcard pattern, not a regular expression: `[epic] =~ [*pi*]` returns `1`. `in` and `not in` test case-insensitive membership in a space-separated word list; write the space in `not in`.

`##` joins strings without inserting a space, `#=` appends to a variable, and `#~` prepends. Binary `#` and compound `##=` are also accepted concatenation forms. Unary `@` counts bytes, not Unicode code points or display columns. Unary `#` counts words using the applicable extraction rules, including `xdebug extractw`.

Unary `*` turns a value into an expando name; unary `**` performs another text-expansion pass. These forms and unary count operators can use the current argument string when their operand is omitted. See [expando substitution](expando_substitution.md) and [lvalues](lval.md) for name lookup and writable destinations.

## Assignments and increment/decrement

Ordinary and compound assignments return the assigned value. Assignment associates right to left: `@ foo += bar *= 2` doubles `bar` before adding its result to `foo`. Unqualified assignment updates a visible local if present, otherwise a global; use `:name` or `::name` to select local or global storage explicitly.

Prefix increment/decrement returns the changed integer value. Suffix increment/decrement changes the variable but returns its previous value:

```irc
@ foo = 5
@ before = foo++
@ after = ++foo
```

The final values are `foo = 7`, `before = 5`, and `after = 7`. A condition such as `foo++ < 10` compares the old value, but the variable is already incremented when the condition finishes, including when the test is false. Separate adjacent operators with spaces: `foo++ + bar` states the grouping clearly.

## Conditional evaluation

`&&` skips its right side when the left is false; `||` skips it when the left is true. `condition ? yes : no` evaluates only the chosen branch. Skipped sections are still parsed, but their function calls, assignments, and lambda bodies do not execute.

`^^` evaluates both sides. `??` chooses the left value when it is true, otherwise the right value, but evaluates both sides; it is not a short-circuit fallback operator. Compound logical assignments also evaluate the right side. The comma operator evaluates both operands for their effects and returns the right value.

```irc
@ foo = 5
@ bar = foo > 3 ? 1 : 0
@ matches = pattern(b* foo bar blah)
```

These assign `1` to `bar` and `bar blah` to `matches`. Function parameters use text mode, not an implicit nested arithmetic expression; parentheses after a function name are required for a call. Bracketed variable-name components also use text expansion: `foo[1][$0]` constructs a dotted name using the current first argument, rather than indexing a Karll array.

## Errors and limits

A non-lvalue assignment target, misplaced operators, missing operands, or malformed grouping can produce diagnostics. Do not depend on a useful result after an error. Earlier assignments or function side effects are not rolled back, and the parser has fixed token and operand-stack limits. Split large expressions into smaller statements when needed.

Operators themselves require no server connection or script package. Called functions and lambda commands can have additional requirements and effects. See [expressions](expressions.md) for operand forms and [functions](functions_overview.md) for function categories.

## Source references

- `source/expr2.c`: lexer, precedence/associativity tables, conversions, operator implementations, and short-circuit parsing.
- `source/expr.c`: expression entry and `canon_number()` formatting.
- `source/alias.c`: assignment storage and lvalue validation.
- `source/ircaux.c`: Boolean tests and numeric/name helpers.
- `source/words.c`: word-list membership.
- `source/functions.c`: function argument expansion and dispatch.
