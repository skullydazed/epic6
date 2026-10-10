# repeat

Execute a command or block a fixed number of times.

## Synopsis

```text
/repeat count command
/repeat (expression) command
/repeat count {commands}
```

## Description

`repeat` executes the supplied command text the requested number of times. A count enclosed in parentheses is evaluated once as an EPIC expression using the current argument context. Otherwise, the first token is taken as count text, with double-quoted token handling but no additional expression evaluation. Ordinary surrounding command expansion can still apply.

The resulting text is converted to an integer with base detection: decimal is usual, a leading `0` selects octal, and `0x` selects hexadecimal. Conversion can stop at a nonnumeric suffix; it is not strict count validation. Missing, nonnumeric, zero, or negative counts perform no repetitions. An unmatched count parenthesis reports `REPEAT: Could not find expression`. Use a reasonably sized positive integer; there is no useful guarantee for values outside the implementation's integer range.

All remaining text is executed anew on each iteration through normal block parsing. Use braces to group multiple statements. Each execution uses the original argument context (`$*`) and the current variable/client state, so variables changed by one iteration can affect expansion in the next. The command does not create a separate local scope or provide an iteration variable. A missing body has no useful effect.

## Control flow and timing

The repetitions run synchronously with no built-in delay or event-loop yield between them. Commands in the body can introduce their own waits. Use [timer](timer.md) for scheduled repetitions and [pause](pause.md) for an explicit script wait when appropriate.

`repeat` does not establish handlers for [break](break.md) or [continue](continue.md). Without an enclosing construct that catches them, those commands report that they cannot be used there. With an enclosing loop, they can propagate to that loop instead of controlling `repeat`. The repeat counter itself does not check or consume break, continue, or return exceptions between calls to its body. Block parsing checks those exceptions only after executing a statement, so a pending exception does not prevent later repeat iterations from executing their first statement again; subsequent statements can then be skipped. Use [for](for.md) or [while](while.md) when you need ordinary loop control.

There are no options, result value, or normal output beyond what the repeated commands produce. The built-in is always registered and needs no IRC connection; the body can contain commands with their own requirements.

## Examples

Display a line three times:

```text
/repeat 3 echo *** The time is $Z
```

Evaluate the count once and update a variable each time:

```text
assign count 2
assign seen 0
repeat (count + 1) {@ seen++; echo $seen}
```

This displays `1`, `2`, and `3`.

## Source

Registration is in [source/commands.c](../../source/commands.c). `repeatcmd` is in [source/if.c](../../source/if.c), count conversion in [source/ircaux.c](../../source/ircaux.c), and repeated block execution and exception propagation in [source/commands.c](../../source/commands.c).
