# do command

## Synopsis

```text
/do <commands>
/do {<commands>}
/do {<commands>} while (<condition>)
```

## Description

`do` has two uses. Without a trailing `while`, it executes command text in the current script scope, similarly to [`eval`](eval.md). With a braced body followed by `while (condition)`, it is a post-test loop: it executes the body before evaluating the condition, so the body runs at least once.

For a loop, braces around the body and parentheses around the condition are required. Keep `while (...)` in the same statement as the closing brace. In a standard-loaded script, put it on the same physical line as `}` so it is not interpreted as a separate command.

After each normal iteration, the condition is evaluated as an expression. An empty result or a numeric zero is false; other results are true. A false result ends the loop. The body and condition use the current positional arguments, and `do` does not create a fresh local-variable scope.

There are no options and no return value. The command produces no output of its own on success, although commands in its body can. With no command text, it does nothing. It is available in every build and needs no server connection unless its body does.

## Control flow and restrictions

[`break`](break.md) exits the do/while loop without evaluating its trailing condition. [`return`](return.md), when valid in the surrounding alias, also stops the loop and propagates to that call.

In the current implementation, [`continue`](continue.md) immediately starts the next body iteration and skips the trailing condition test. This differs from reaching the end of the body normally. If every iteration continues, the condition cannot end the loop.

The non-loop forms do not install their own break or continue handler. Such commands can still affect an enclosing active loop.

An unbalanced opening brace reports `DO: unbalanced {`; a recognized trailing `while` without a usable parenthesized condition reports `DO: unbalanced (`. Neither malformed loop is executed.

Do not append other words after a standalone braced body or extra commands after the loop condition. The handler does not treat arbitrary trailing text consistently: after a braced non-loop body, the first trailing word is consumed while checking for `while`, and the remainder can execute as commands; after a recognized loop, trailing text is ignored. Use separate statements instead.

The command-text form adds another parsing/expansion pass. Braced text protects its contents until execution; unbraced text can be expanded before `do` receives it and again when run. Use this deliberately when constructing commands, rather than assuming literal text is preserved.

## Examples

To show a variable's value in a script:

```text
assign foo example
do { echo The variable foo expands to $foo }
```

To run a body at least once and then repeat while the condition is true:

```text
@ count = 0
do {
    @ count++
    echo Iteration $count
} while (count < 3)
```

This displays iterations 1, 2, and 3. At the interactive input prompt, use a leading `/` for command statements.

## See also

[`eval`](eval.md), [`while`](while.md), [`until`](until.md), [`break`](break.md), [`continue`](continue.md), and [`return`](return.md).

## Source

See [source/if.c](../../source/if.c) for `docmd()`, [source/commands.c](../../source/commands.c) for `evalcmd()` and command/block execution, [source/expr.c](../../source/expr.c) for expansion, and [source/ircaux.c](../../source/ircaux.c) for condition truth testing in `check_val()`.
