# on input

## Synopsis

```text
/on [<noise>]input "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the [send_line](../bind/send_line.md) key action submits the input buffer, normally when you press Enter. It sees both commands and ordinary text intended for the current channel or query, before the interactive statement parser handles them.

This is a powerful way to preprocess submitted input. A suppressing handler takes responsibility for processing or discarding the line; a broad handler that does nothing can prevent normal commands and messages from being executed.

The event is not generated for every outgoing message or script command. For example, [sendline](../commands/sendline.md) directly invokes the interactive parser without invoking `on input`. When a line-input prompt is active, submission goes to that prompt's callback instead of this hook.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$*` | Submitted input-line text, including a leading command prefix when present. |

The entire line is offered for standard [on](../commands/on.md) pattern matching. An empty submitted line still reaches the hook when no line-input prompt intercepts it. There are no event-specific options.

## Default behavior and effects

Before the hook runs, EPIC copies the input line, clears its buffer, resets the input cursor and horizontal position, and redraws input. The hook executes in the current window's server context. Suppression does not put the text back in the input buffer.

If the hook permits default handling, EPIC parses the copied text as interactive input: ordinary text goes to the current target and commands are executed. Resulting IRC messages require a suitable connection and target, and their delivery depends on the server. If a serial-zero suppressing hook such as `^input` matches, EPIC skips that parser call. The hook has no separate built-in display.

Submitting input can also advance held output or scrollback through the `send_line` action, even when this hook suppresses parsing. See [send_line](../bind/send_line.md) for those effects.

This hook is marked nonrecursive: another input-hook dispatch while it is already running does not run its handlers again. That does not prevent a script from repeatedly calling its own aliases or sending messages.

## Example

Preprocess the submitted line through the command evaluator:

```text
on ^input "*" {
    eval $*;
};
```

This replaces ordinary interactive parsing with [eval](../commands/eval.md), allowing script expression expansion and command evaluation. It changes how all matched input is interpreted; it is not needed for ordinary submission. To process a replacement line with normal interactive rules inside a handler, use `sendline <replacement text>` instead.

## Source references

- [source/input.c](../../source/input.c): `send_line` clears input, selects prompt versus hook handling, conditionally parses the line, and applies hold/scrollback effects.
- [source/commands.c](../../source/commands.c): `sendlinecmd` invokes interactive parsing without this hook; `evalcmd` evaluates script commands; `parse_statement` implements statement parsing.
- [source/hook.c](../../source/hook.c): the `input` registration sets the nonrecursion flag; hook dispatch implements matching and default suppression.
