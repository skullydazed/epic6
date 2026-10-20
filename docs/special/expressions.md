# Expression syntax in EPIC

## Expression mode and text mode

In text mode, a statement names a command and supplies text arguments; `$` forms perform [expando substitution](expando_substitution.md). In expression mode, text is interpreted as operands and operators, producing one result.

Use `@ expression` or a statement starting with `(` to evaluate an expression and discard its result. Use `${expression}` in expanded text to insert the result. Conditional and loop commands also evaluate expressions in their designated condition fields, including `if`, `unless`, `while`, `until`, `do ... while`, and the condition of a C-style `for` loop.

Write variable names without `$` in expressions: `@ total = count + 1`. A leading dollar sign is skipped by the expression lexer; it is unnecessary for an ordinary variable reference. Function arguments are text, not an implicit nested expression: `strlen($name)` expands `$name`, whereas `strlen(name)` measures the literal word `name`.

## Operators and operands

An operator combines one or more operands into a new value. Precedence determines which operator binds first, and associativity resolves operators at the same precedence. Parentheses override the normal grouping and may be nested.

For example, `3 - 4 * 5 - 7` first becomes `3 - 20 - 7`, then `-17 - 7`, and finally `-24`. Multiplication binds before subtraction; subtraction associates from left to right. Assignment and exponentiation associate from right to left. An expression as a whole is not simply evaluated right to left.

### Operators and associativity

Smaller precedence numbers bind more tightly. Prefix and suffix describe an operator's position relative to its operand.

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

`<=>` returns a negative number, zero, or a positive number according to string ordering. It does not swap variables. Write `not in` with a space between the words.

## How operands are handled

Operands can be numeric literals, variable references, string operands, function results, or grouped expressions. A variable reference supplies its value; on the left of assignment it identifies the variable to change. Assignment and increment/decrement need a writable variable name, not an arbitrary string result. Undefined variables normally supply an empty string.

Values are converted as an operator needs them. Arithmetic uses floating-point values, except integer operations such as remainder, bitwise operations, shifts, and increment/decrement. `%` converts both operands to integers. Exponentiation uses the platform math library; invalid domains and extreme values are subject to that library's numeric behavior.

For Boolean tests, an empty string and a string consisting entirely of numeric zero are false. Other strings are true, including words such as `off` and strings with text following a leading zero. Logical operators return `0` or `1`.

`==`, `!=`, `<`, `<=`, `>`, and `>=` compare numerically when both values are recognized as real numbers; otherwise they compare strings without case sensitivity. `===` and `!==` always compare exact, case-sensitive strings, so `[01] == [1]` is true but `[01] === [1]` is false.

Floating-point results are converted back to text using fixed-decimal formatting. With [floating_point_math](../set/floating_point_math.md) on, redundant trailing fractional zeros are removed; with it off, the fractional portion is discarded when the value becomes text. The `floating_point_precision` setting does not control this parser's formatting. Do not use expression results as an arbitrary-precision number representation.

## Escapes to text mode

| Form in an expression | Treatment |
| --- | --- |
| `[text]` | Text operand with expando substitution when its value is needed. |
| `"text"` | Text operand with expando substitution when its value is needed. |
| `'text'` | Literal text without expando substitution; an escaped single quote is dequoted. |
| `{commands}` | Execute commands as a lambda function and use its return value; this is not a literal string operand. |

Function parameters also use text mode and undergo expansion before the call. Built-in and script-defined functions are available. Bracketed portions of variable and function names can construct names dynamically; see [arrays](arrays.md) and [expandos](expandos.md).

## EPIC-specific operators

`##` joins strings without adding a space. `#=` appends to a variable and `#~` prepends:

```irc
@ foo = [foo] ## [bar]
@ foo #= [blah]
@ foo #~ [hmm]
```

The final value is `hmmfoobarblah`. Ordinary assignment and compound assignment return the value assigned, allowing chains such as `@ foo += bar *= 2`.

For wildcard matching, the right operand is the pattern: `[epic] =~ [*pi*]` and `[epic] !~ [*z*]` both return `1`. These are wildcard patterns, not regular expressions. `in` and `not in` compare the left value against space-separated words in the right value, without case sensitivity; `[bar] in [foo bar blah]` returns `1`.

Unary `@` counts bytes, not Unicode code points; use `strlen()` for code-point counts. Unary `#` counts words according to the applicable extraction rules, including `xdebug extractw`. Unary `*` interprets its operand as an expando name; unary `**` performs another text-expansion pass. These unary forms and the count operators can use the current argument string when their operand is omitted.

`&&` and `||` skip evaluation of the right operand when the left determines the result. `?:` evaluates only the chosen branch. Skipped portions are still parsed, but their functions, assignments, and lambda bodies do not run. `^^` evaluates both sides. `??` also evaluates both sides: it selects a value, but is not a short-circuit default operator. Compound logical assignments evaluate their right operands.

## Increment, decrement, and examples

Prefix `++foo` changes `foo` and returns the new integer value; suffix `foo++` changes it and returns the old value. Use spaces around adjacent operators: `foo++ + bar` clearly states the intended grouping.

```irc
@ foo = 3
@ result = foo * (4 + 5)
@ label = foo > 2 ? [large] : [small]
@ matches = pattern(b* foo bar blah)
```

These set `result` to `27`, `label` to `large`, and `matches` to `bar blah`. To display an expression result from a script, use a statement such as `echo Result: ${foo * (4 + 5)}`.

## Errors and limitations

Malformed expressions can produce diagnostics and an empty result; division or integer remainder by zero produces a diagnostic and an empty operand. Evaluation is not transactional: assignments and function side effects performed before an error are not rolled back. Do not depend on a useful result after an error.

The parser has fixed internal token and operand-stack limits, so split very large expressions into smaller statements. Parentheses make grouping explicit, but do not remove these limits. No IRC request is sent by expression evaluation itself; called functions or lambda commands can have their own effects.

## Source references

- `source/expr.c`: `parse_inline()` and entry from `${...}`.
- `source/expr2.c`: operator precedence, lexer, conversions, short-circuit handling, and evaluation.
- `source/commands.c` and `source/if.c`: expression statements and control-flow callers.
- `source/functions.c`: function invocation and argument expansion.
- `source/ircaux.c`: `check_val()` and numeric/string helpers.
- `source/words.c`: word-list membership.
