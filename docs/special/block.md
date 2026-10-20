# Blocks

## Description

A **block** is a sequence of [statements](statements.md) separated by semicolons. A **block statement** groups such a sequence inside curly braces:

```text
{
    echo one;
    echo two;
}
```

The statements run in order when the surrounding command or execution context evaluates the block. Commands such as [alias](../commands/alias.md), [if](../commands/if.md), and [on](../commands/on.md) use blocks as bodies. Their own syntax determines whether braces are required or optional; do not assume braces can be omitted wherever a command accepts a body.

A final semicolon is permitted but not required for the final statement. Formatting a block over several lines improves readability, but a newline is not universally a replacement for a semicolon in the command evaluator. Empty statements have no effect.

## Statement boundaries and quoting

The block splitter recognizes semicolons at the current level. Semicolons within balanced parentheses or nested braces remain part of that nested construct until it is evaluated. A backslash protects the following character from this boundary scan. Double quotes alone do **not** protect a semicolon from the block splitter.

For example, in a script block, use an escaped semicolon to include one in an echo argument:

```text
alias punctuation {
    echo one\; two;
    echo three;
};
```

The first echo prints `one; two`; the second prints `three`. This is client script parsing, not shell quoting. Expansion and a command's own argument parser can introduce additional interpretation; see [ircII syntax](ircii_syntax.md).

## Execution and scope

Braces group commands but do not by themselves create an [atomic scope](atomic_scope.md). Local variables follow the scope of the enclosing alias, hook, loader, or callback. A nested alias invocation can create a new scope even when it is called from within the same block.

Execution stops when a handled `return`, `break`, or `continue` applies to the enclosing construct, or when the client records a system exception. A block is not a transaction: earlier commands are not undone when later execution stops. It has no separate function-return value or automatic success result; the commands and surrounding construct provide those effects.

The interactive input path is distinct from block execution. Ordinary submitted input is initially parsed as one statement, with command-prefix and message-sending rules. Do not assume typing an unadorned semicolon-separated line at the input prompt evaluates it as a script block. Use a script body or an explicit evaluation command when script interpretation is intended.

Unbalanced delimiters can produce parser diagnostics. Some paths attempt to continue with the remaining text rather than rejecting the entire block, so malformed grouping should not be used as an execution barrier. Blocks require no script package, external program, server request, or optional platform feature; individual statements may require or cause those things.

## Example

```text
alias two_lines {
    echo one;
    echo two;
};
two_lines
```

Calling `two_lines` prints `one` followed by `two`. The alias invocation creates its local scope; the braces delimit its body.

## Source references

- [source/expr.c](../../source/expr.c): `next_statement` scans semicolons, nested parentheses/braces, and backslash escapes; expansion handles escaped characters.
- [source/commands.c](../../source/commands.c): `parse_block` runs statements and stops on applicable control flow; `parse_statement` distinguishes block, expression, command, and interactive input paths.
- [source/alias.c](../../source/alias.c): local-frame creation and destruction are independent of ordinary braces.
