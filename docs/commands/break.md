# break command

## Synopsis

```text
/break
```

## Description

`break`, like its C counterpart, exits a loop early. It stops the remaining commands in the current body and leaves the innermost active construct that handles a break. Execution then continues after that construct.

Supported handlers include [`while`](while.md), [`until`](until.md), [`do`](do.md) with `while`, [`for`](for.md), [`foreach`](foreach.md), and the [`fe`](fe.md)/[`fec`](fec.md) family. A matching [`switch`](switch.md) case also handles `break`: it stops the case body without breaking an enclosing loop.

There are no options or required arguments. Supplied arguments are ignored; `break 2` does not exit two nesting levels. On success, the command produces no output. It is available in every build and requires no server connection.

## Restrictions

Outside an active break-handling construct, the command prints `Cannot BREAK here.` and does not interrupt execution. A plain block or an alias definition is not itself a break handler.

The context is determined at execution time. A `break` inside an alias called by a running loop can propagate out through the call and exit that loop, unless a nearer construct handles it. Do not assume an alias boundary prevents that effect.

Use [`continue`](continue.md) to skip to another iteration, or [`return`](return.md) to finish an alias call. `break` does not return a value.

## Examples

This otherwise endless loop stops after 20 iterations:

```text
@ loop = 0
while (1) {
    @ loop++
    if (loop == 20) { break }
}
echo Completed $loop iterations
```

Inside a command block, statements after the break in that body are skipped:

```text
while (1) { echo Once; break; echo This is skipped }
echo Execution continues here
```

These are script examples; at the interactive prompt, use the leading `/` for command statements.

## See also

[`alias`](alias.md), [`continue`](continue.md), [`return`](return.md), [`for`](for.md), [`while`](while.md), [`until`](until.md), and [`switch`](switch.md).

## Source

See [source/commands.c](../../source/commands.c) for the `break` registration, `breakcmd()` error handling, and `parse_block()` propagation. [source/if.c](../../source/if.c) implements the loop and switch handlers that enable and consume breaks.
