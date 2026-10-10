# if

Evaluate a condition and execute the first applicable branch.

## Synopsis

```text
/if (condition) command
/if (condition) { commands }
/if (condition) { commands } { alternative commands }
/if (condition) { commands } else { alternative commands }
/if (condition) { commands } elsif (condition) { commands } else { commands }
```

`elif` and `else if` are also accepted in place of `elsif`.

## Description

`if` evaluates its parenthesized condition as an EPIC expression. A true result executes the following command or brace-delimited block. A false result proceeds to a chained condition or an optional alternative. Only the first selected branch executes; later conditions are not evaluated after a branch is selected.

An empty result or a string wholly representing numeric zero is false. Nonzero numbers and other nonempty strings are true. Thus `if (foo)` tests the value of `foo`, not merely whether it exists: a variable containing `0` is false. `if (!foo)` tests the opposite.

Bare names in an expression refer to variables, numbers are constants, and bracketed text such as `[hello]` is a string. Use explicit expandos where needed: `if ([$0])` tests the first enclosing argument, whereas `if (0)` tests the constant zero. Functions may be called in conditions. The expression uses the enclosing argument context.

| Expression | Test |
| --- | --- |
| `foo` | Whether the variable's value is true |
| `!foo` | Whether it is false |
| `a == b`, `a != b` | Equality or inequality |
| `a < b`, `a > b`, `a <= b`, `a >= b` | Ordered comparison |
| `a && b` | Both are true |
| `a \|\| b` | At least one is true |
| `a ^^ b` | Exactly one is true |

Comparisons are numeric when both operands are numeric; otherwise they compare strings case-insensitively. Logical `&&` and `||` short-circuit their operands. Parentheses can group expressions.

Assignments are allowed inside the expression without the `@` command prefix. Use explicit grouping to make the intended order clear: `if ((foo = 3) > bar)` assigns `3` to `foo`, then compares it with `bar`. Assignment follows ordinary variable-scope rules.

The alternative is optional. Without braces, the whole remaining command text is the true branch, so use braces when supplying an alternative or chained conditions. Empty blocks are allowed. Keep a space after `else`, `elif`, or `elsif`, and write the two-word spelling as `else if` with a single separating space; the parser recognizes those literal keyword prefixes. The selected body runs in the current scope, with no new local scope or loop-control catcher. Break, continue, and return propagate to their enclosing contexts.

## Examples

These forms perform the same comparison:

```text
if (foo == bar) echo foo and bar are the same
if (foo == bar) {
    echo foo and bar are the same
}
```

Select among several cases:

```text
if (count > 10) {
    echo many
} elsif (count > 0) {
    echo some
} else {
    echo none
}
```

Test a function result:

```text
if (rmatch(foobar *blah* *bar)) {
    echo it matched
}
```

## Related commands and restrictions

[unless](unless.md) shares the handler and reverses the condition test. `if (!foo) { ... }` and `unless (foo) { ... }` therefore choose their first branch under the same circumstances.

Conditions require parentheses. Malformed expressions and unbalanced parentheses or braces produce diagnostics. With an empty argument string, the built-in performs no action. It has no options, produces no ordinary output itself, and is always registered. Expansion diagnostics can be enabled through the relevant debug setting.

## Source

Registration is in [source/commands.c](../../source/commands.c), and `ifcmd`, branch parsing, and the `unless` distinction are in [source/if.c](../../source/if.c). Expression evaluation and operators are in [source/expr.c](../../source/expr.c) and [source/expr2.c](../../source/expr2.c); final truth testing is in [source/ircaux.c](../../source/ircaux.c).
