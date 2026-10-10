# continue command

## Synopsis

```text
/continue
```

## Description

`continue` ends the current loop iteration early, skipping the remaining commands in that body. The innermost active loop that handles a continue then proceeds with its next iteration.

Supported loops include [`do`](do.md) with `while`, [`while`](while.md), [`until`](until.md), [`foreach`](foreach.md), the [`fe`](fe.md)/[`fec`](fec.md) family, and [`for`](for.md) forms using a numeric range, a list, or parenthesized initialization/test/iteration statements.

For `while` and `until`, the condition is tested again. Numeric-range and list forms of `for` advance normally. In parenthesized `for`, the iteration statement still runs before the next condition test. `foreach`, `fe`, and `fec` advance to their next items; mapping variants collect the current variables before handling the continue.

There is one important difference from reaching the end normally: in the current `do { ... } while (...)` implementation, `continue` immediately restarts the body and skips that iteration's trailing condition test. A continue reached on every iteration can therefore prevent the condition from ending that loop.

There are no options or required arguments, and supplied arguments are ignored. The command produces no output on success, is available in every build, and requires no IRC connection.

## Restrictions

Outside an active continue-handling loop, the command prints `Cannot CONTINUE here.` and does not interrupt execution. A plain block, alias call, or `switch` case is not itself a continue handler.

The context is determined at execution time. A continue inside an alias called from a running loop can propagate through that call and skip the rest of the enclosing loop's iteration, unless an inner loop handles it first. A continue inside a switch case can likewise reach an enclosing loop.

Use [`break`](break.md) to leave a loop instead of advancing it, or [`return`](return.md) to finish an alias call. `continue` does not return a value.

## Example

This script displays the numbers 0 through 10 except 4. The `@ loop++` iteration statement still executes after the continue:

```text
for (@ loop = 0, loop <= 10, @ loop++) {
    if (loop == 4) { continue }
    echo $loop of 10, skipping 4.
}
```

At the interactive input prompt, prefix command statements with `/`.

## History

The `continue` command first appeared in EPIC4pre1.031.

## Source

See [source/commands.c](../../source/commands.c) for the registration, `continuecmd()` error handling, and `parse_block()` propagation. [source/if.c](../../source/if.c) implements loop-specific handling in `docmd()`, `whilecmd()`, `foreach()`, `fe()`, `for_next_cmd()`, `for_fe_cmd()`, and `loopcmd()`.
