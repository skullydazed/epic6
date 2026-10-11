# while command

## Synopsis

```text
/while (condition) { commands }
/while (condition) command
```

## Description

`while` combines the repetition of [`for`](for.md) with the expression test of [`if`](if.md). It evaluates the condition before each iteration and executes the body while that condition is true. If the initial condition is false, the body is never executed.

The condition can contain comparisons, assignments, function calls, or other expressions accepted by `if`. It is evaluated afresh on each test, using the enclosing argument context. An empty result or a string wholly representing numeric zero is false; nonzero numbers and other nonempty strings are true. Bare names refer to variable values. Empty parentheses therefore select no iterations.

The body is command text, expanded and executed on each iteration in the current scope. No new local scope is introduced. Braces are recommended for grouping several commands but are optional when the body is the remaining command text. If a braced body is successfully parsed, following text is ignored by this loop.

[`continue`](continue.md) skips the rest of the body and retests the condition. [`break`](break.md) exits the loop. The innermost active loop handles those exceptions; return and system exceptions exit this loop and propagate to enclosing contexts.

## Examples

To display a warning message three times:

```text
@ xx = 3
while (xx > 0) {
    echo WARNING! This ship will self destruct in $xx seconds!
    @ xx--
}
```

This prints three messages immediately; it does not wait a second between them. Use [`timer`](timer.md) to schedule delayed work.

An infinite loop behaving like the Unix `yes` command:

```text
while (1) echo yes
```

This keeps printing until interrupted.

## Related commands and restrictions

[`until`](until.md) uses the same loop machinery but runs while its condition is false. `while (!condition)` and `until (condition)` therefore select iterations under the same circumstances. `for` offers range, list, and initialization/test/iteration forms; `while` has only a condition and body, so any initialization or increment commands must be arranged separately.

Parentheses are required around the condition. Missing or unbalanced delimiters produce diagnostics. An empty body is allowed, but a true condition with no way to become false can run indefinitely. There are no options, normal completion output, or function return value. The command is always registered and does not require an IRC connection, although body commands may require one.

The loop runs synchronously without an automatic delay or event-loop wait. Make progress within the loop rather than depending on later IRC traffic to change the condition; a nonterminating loop can keep EPIC busy indefinitely.

## Source

[source/commands.c](../../source/commands.c) registers `while` and implements `runcmds()`. [source/if.c](../../source/if.c) implements `whilecmd()`, its distinction from `until`, expression/body parsing, and control-flow handling. [source/ircaux.c](../../source/ircaux.c) implements `check_val()`; [source/expr.c](../../source/expr.c) and [source/expr2.c](../../source/expr2.c) implement expressions.
