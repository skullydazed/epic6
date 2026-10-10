# unless command

## Synopsis

```text
/unless (condition) command
/unless (condition) { commands } [{ alternative commands }]
/unless (condition) { commands } else { alternative commands }
/unless (condition) { commands } elsif (condition) { commands } else { commands }
```

`elif` and `else if` are also accepted in place of `elsif`.

## Description

`unless` is the inverse of [`if`](if.md): when its parenthesized condition is false, it executes the following command or block. When the condition is true, it proceeds to any chained condition or final alternative. Only the first selected branch executes.

The inversion applies to every chained condition. Even though the keywords are spelled `elsif`, `elif`, or `else if`, a chain starting with `unless` keeps looking for a **false** condition. The final `else` executes only when every tested condition was true. Later conditions are not evaluated after a branch is selected.

Conditions are EPIC expressions, evaluated with the enclosing argument context. Bare names refer to variable values; bracketed text is a string. An empty result or a string wholly representing numeric zero is false; nonzero numbers and other nonempty strings are true. Thus `unless (foo)` tests whether `foo` has a false value, not simply whether the variable is undefined. See [`if`](if.md) for expression operators, assignments, and further syntax details.

Use braces when supplying chained conditions or alternatives. Without braces, all remaining text is the first branch's command. Empty blocks are allowed. Keep a space after each branch keyword, and use a single separating space in `else if`, because these spellings are recognized as literal prefixes. An alternative block can follow directly without the word `else`.

The selected body runs in the current scope; the command does not introduce a local scope or catch break, continue, or return. Those control commands propagate to their enclosing contexts. There are no options or ordinary output from `unless` itself, and no function return value. An empty argument string does nothing. Missing or unbalanced expression delimiters produce diagnostics. The command is available in every build and requires no IRC connection.

## Example

```text
unless (1 == 1) {
    echo 1 == 1. This is not executed, because 1 == 1 is true.
} elsif (1 == 0) {
    echo 1 == 0. This is executed because 1 == 0 is false.
} else {
    echo This is not executed because a false condition was already found.
}
```

The first condition is true, so its body is skipped. The second condition is false, so its body runs and the final alternative is skipped.

## History

The `unless` command first appeared in EPIC3.

## Source

[source/commands.c](../../source/commands.c) registers `unless`. [source/if.c](../../source/if.c) implements `ifcmd()`, the persistent inversion flag, and branch parsing. [source/expr.c](../../source/expr.c) and [source/expr2.c](../../source/expr2.c) implement expression evaluation; [source/ircaux.c](../../source/ircaux.c) implements the final `check_val()` truth test.
