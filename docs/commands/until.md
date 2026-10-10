# until command

## Synopsis

```text
/until (condition) { commands }
/until (condition) command
```

## Description

`until` is the inverse of [`while`](while.md): it performs its body while the condition is false. The condition is tested before each iteration, so a true initial condition prevents the body from running at all.

The parenthesized condition is evaluated as an EPIC expression each time it is tested. The body is evaluated as command text each time it executes. Both use the enclosing argument context; the body runs in the current scope without introducing new local variables. Braces are recommended for grouping multiple commands but are optional for a body consisting of the remaining command text.

An empty expression result or a string wholly representing numeric zero is false; nonzero numbers and other nonempty strings are true. Bare names refer to variables, so `until (done)` runs until the value of `done` becomes true. See [`if`](if.md) for expression syntax and truth rules.

[`continue`](continue.md) skips the rest of the body and retests the condition. [`break`](break.md) exits the loop. These exceptions are handled by the innermost active loop; return and system exceptions exit this loop and propagate to their enclosing contexts.

There are no options, ordinary output, or function return value from the command itself. Parentheses are required around the condition; missing or unbalanced delimiters produce diagnostics, which may refer to `WHILE` because the commands share a handler. When a braced body is successfully parsed, following text is not executed by this loop. An empty body is allowed. Empty parentheses yield a false result, so `until () {}` can run indefinitely.

## Examples

To display a warning message three times:

```text
@ xx = 3
until (xx == 0) {
    echo WARNING! This ship will self destruct in $xx seconds!
    @ xx--
}
```

This prints three messages immediately; it does not wait a second between them. Use [`timer`](timer.md) for scheduled work.

The following behaves like the Unix `yes` command and keeps printing until interrupted:

```text
until (0) echo yes
```

## Restrictions

The loop executes synchronously without an automatic delay or event-loop wait. A condition that never becomes true, combined with no terminating control command, can keep EPIC busy indefinitely. Arrange for the loop itself to make progress rather than depending on a later incoming IRC message to change its condition.

The command is available in every build and requires no IRC connection, although commands in its body may require one.

## History

The `until` command first appeared in EPIC3.

## Source

[source/commands.c](../../source/commands.c) registers `until` and implements `runcmds()`. [source/if.c](../../source/if.c) implements `whilecmd()`, the inverted condition test, body parsing, and control-flow handling. [source/ircaux.c](../../source/ircaux.c) implements `check_val()`; [source/expr.c](../../source/expr.c) and [source/expr2.c](../../source/expr2.c) evaluate expressions.
