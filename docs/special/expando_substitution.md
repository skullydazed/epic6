# Expando substitutions

Expando substitution replaces `$` forms in a string with values and removes backslashes used to protect characters. Expandos can read variables, select arguments, call functions, and evaluate expressions.

## Where expansion happens

Script command statements are expanded before their commands run. Ordinary interactive input does not receive this general expansion pass; a command may still expand particular arguments itself. Use [eval](../commands/eval.md) when you deliberately want another evaluation pass.

Other expansion contexts include function arguments, expression variable names, the contents of string operands written as `[...]` or `"..."`, [argument-list](arglist.md) defaults, and the contents of `$()`. Expansion also applies to flexible hook patterns, `fe`/`fec` lists, `for ... in` lists, and `switch` control strings and matching patterns. The expansion-enabled form of `$cparse()` performs a further pass after its formatting substitutions.

Parentheses and braces without a preceding `$` protect their contents from the current general expansion pass. This lets commands receive bodies or expressions that they will evaluate later. Protection for one pass does not mean the text will never be expanded.

## Forms

| Form | Result |
| --- | --- |
| `$name` | The value of a variable, built-in expando, setting, or environment variable. |
| `$function(arguments)` | The function's return value; its arguments are expanded before the call. |
| `$*` | The complete argument string supplied to this evaluation. |
| `$0`, `$1`, … | A numbered argument, starting at zero. |
| `$n-m`, `$n-`, `$-m` | An inclusive argument range, from argument `n` onward, or through argument `m`. |
| `$~` | The last argument. |
| `$#value` | The word count of the following expando's value; `$#` alone counts the current arguments. |
| `$@value` | The byte length of the following expando's value; `$@` alone measures the current argument string. |
| `$(text)` | Expand `text`, then interpret the result as an expando name and retrieve its value. |
| `${expression}` | Evaluate an expression and substitute its result. |
| `$$` | A literal dollar sign. |
| `$"prompt"` | Display a prompt, wait for a complete input line, and substitute that line. |
| `$'prompt'` | Display a prompt, wait for a key, and substitute the response. |

Here `value` is written without another dollar sign: for example, `$#names` counts words in `$names`. Argument extraction and word counting depend on the applicable word-extraction settings, including `xdebug extractw`; do not assume that quoted text is always treated as one argument. Missing arguments and undefined values normally expand to an empty string. A lone dollar sign at the end of the string expands away.

`$@value` counts UTF-8 bytes, whereas `$strlen(value)` counts Unicode code points. Neither is a general measure of terminal display width.

## Variable lookup

An ordinary name is looked up among visible local variables, global variables, built-in expandos, and built-in settings, in that order. If no value is found, lookup falls back to the process environment, using the original spelling of the requested environment name. EPIC names are case-insensitive; environment names may be case-sensitive.

`$:name` requests local lookup, but the current implementation still falls back to the global/built-in symbol table if no local value is found. `$::name` skips local variables. The environment fallback retains the literal requested spelling, including any leading colons. See [atomic scope](atomic_scope.md) for local-variable visibility. Bracketed portions of variable names can themselves be expanded to construct a name.

## Modifiers

A width modifier follows the dollar sign: `$[10]name` pads or truncates the result to a field width of ten and left-justifies it; `$[-10]name` right-justifies it. Padding uses [pad_char](../set/pad_char.md). Zero leaves the width unchanged. A width beginning with `$` is expanded first, as in `$[$width]name`; otherwise it is read as an integer, not evaluated as an arithmetic expression.

Before the width or expando, any number of `^character` modifiers select characters to escape with a backslash in the result. For example, `$^;name` escapes semicolons found in the value of `name`. Width formatting happens before this escaping. These modifiers protect only the selected characters; they do not provide universal quoting for later commands or expressions.

## Protection and expression mode

A backslash protects the next character during an expansion pass and is then removed. For example, `\$name` produces literal `$name` for that pass. Double quotes alone do not suppress general expando substitution. Balanced plain `(…)` and `{…}` are copied without expanding their contents during that pass; unmatched delimiters can produce diagnostics.

Substituted values are appended to the result without being scanned again automatically. Explicit indirection or another evaluation step is needed to interpret dollar signs contained in a value. `$()` repeatedly expands its interior while the result begins with `$`, so avoid self-referential indirect names. The `xdebug slash_hack` compatibility flag changes this form's result insertion.

`${...}` enters expression evaluation, where operators and expression string operands have their own rules. Plain `{...}` delays expansion instead. See [ircII syntax](ircii_syntax.md) and [blocks](block.md). Input-prompt expandos wait synchronously while the event loop continues processing events, so they can interact with other waiting operations.

## Examples

Enter these definitions and then invoke the aliases:

```irc
alias greet { echo Hello, $0! You supplied $# arguments: $* }
alias total { echo Total: ${2 + 3} }
alias indirect { assign destination answer; assign answer forty-two; echo $($destination) }
alias columns { echo |$[12]0|$[-12]1| }
```

`/greet Alice Bob` prints a greeting for Alice and reports two arguments. `/total` prints `Total: 5`; `/indirect` prints `forty-two`. `/columns left right` demonstrates padding and justification.

## Source references

- `source/expr.c`: `expand_alias()`, `alias_special_char()`, and `TruncateAndEscape()`.
- `source/alias.c`: variable lookup, bracketed names, and argument defaults.
- `source/commands.c`: script statement expansion and interactive parsing.
- `source/functions.c`: function argument expansion, `$strlen()`, and `$cparse()`.
- `source/if.c` and `source/hook.c`: control-flow strings and flexible hook patterns.
