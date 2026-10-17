# on exec_errors

## Synopsis

```text
/on [<noise>]exec_errors "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for a line read from the standard error of a process started with [exec](../commands/exec.md), unless that process has a nonempty `-error` callback. This identifies the stream, not whether the program has failed: programs can write informational text to stderr too. Standard output uses [on exec](exec.md); process termination uses [on exec_exit](exec_exit.md).

The current reader is line-buffered, so incomplete output without a newline waits for more data rather than triggering this event immediately. The partial-line branch is disabled. Use the standard [on](../commands/on.md) matching and noise controls; there are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Process logical name, if assigned; otherwise its numeric exec refnum. |
| `$1-` | A line of the process's stderr output. |

The numeric refnum has no leading `%` and is not the operating-system process ID. Trailing carriage returns and newlines are stripped, and the text undergoes incoming character-set conversion configured for the process. The pattern matches the identifier and line together.

## Default behavior and callbacks

If the hook permits default handling, EPIC displays the line without an error label, unless the process's redirect setting is active. A suppressing hook, such as a serial-zero `^exec_errors` hook, prevents that default line and can supply replacement output.

A nonempty `exec -error { ... }` callback runs instead of this hook and its default display. It receives the line without the process identifier prepended. The stdout `-line` callback does not replace this stderr hook. Output runs in the process's stored server context, using its output window when assigned, at the `other` message level.

The `-out`, `-msg`, and `-notice` options set redirect metadata and suppress default local display without disabling the hook. In the current output reader, that metadata does not itself forward stderr lines to IRC: there is no redirect send step. Scripts must explicitly send lines if wanted.

Suppressing display does not stop the process, stop reading its output, or prevent the line from counting toward output limits. [Set shell_limit](../set/shell_limit.md) and the process's `-limit` value can stop further output handling; stdout and stderr contribute to the same received-line count.

## Example

Distinguish stderr from other window output:

```text
on ^exec_errors "*" {
    echo [$0 ERR] $1-;
};
```

This replaces ordinary local display for processes without a nonempty `-error` callback.

## Source references

- [source/exec.c](../../source/exec.c): `do_exec` routes stderr to `exec_errors` and applies output limits; `handle_filedesc` selects `stderrc`, strips line endings, converts encoding, invokes callbacks or hooks, and controls display; `set_context_for_process` selects output context; option handling records `-error` and redirect metadata.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
