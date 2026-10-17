# on exec

## Synopsis

```text
/on [<noise>]exec "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for a line read from the standard output of a process started with [exec](../commands/exec.md), unless that process has a nonempty `-line` callback. Standard error uses [on exec_errors](exec_errors.md) instead. Process termination uses [on exec_exit](exec_exit.md).

The current reader is line-buffered: incomplete output without a newline waits for more data rather than generating an `exec` event immediately. The old partial-line hook branch is disabled. There are no hook-specific options; use the standard [on](../commands/on.md) matching and noise controls.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Process logical name, if assigned; otherwise its numeric exec refnum. |
| `$1-` | The line of standard output. |

The numeric refnum is supplied without a leading `%`; it is not the operating-system process ID. Trailing carriage returns and newlines are stripped before the event, and process output is converted using its configured incoming encoding. The pattern matches the identifier and line together.

## Default behavior and callbacks

If the hook permits default handling, EPIC displays the line without adding a process label, unless the process's redirect setting is active. A suppressing hook, such as a serial-zero `^exec` hook, prevents that default line and can provide replacement output.

A nonempty `exec -line { ... }` callback takes precedence: it runs instead of this hook and its default display. The callback receives the line itself, without the process identifier prepended. The hook runs in the process's stored server context, using its output window when one is assigned, at the `other` message level.

The `-out`, `-msg`, and `-notice` options set redirect metadata and suppress this default local display while leaving the hook eligible to run. In the current output reader, those settings do not themselves forward the line to IRC: the handler contains no redirect send step. A script must explicitly send output if that behavior is wanted. Suppressing this hook does not stop the child process or stop its output from being read.

Process output limits, including [set shell_limit](../set/shell_limit.md) and the process's `-limit` value, can stop further output handling. The line count is updated before the callback or hook, so suppressing display does not avoid the limit.

## Example

Distinguish process output from other window output:

```text
on ^exec "*" {
    echo [$0] $1-;
};
```

This replaces the ordinary local line for processes without a `-line` callback.

## Source references

- [source/exec.c](../../source/exec.c): `do_exec` selects stdout versus stderr and applies output limits; `handle_filedesc` reads lines, strips line endings, converts encoding, chooses callbacks or hooks, and controls default display; `set_context_for_process` selects output context; exec option handling records callback and redirect settings.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
