# set shell_limit

## Synopsis

```text
/set shell_limit [<number>]
```

Default: `0`, meaning no global output limit. With no number, [set](../commands/set.md) displays the current value. Values must be nonnegative integers; negative and nonnumeric values are rejected.

## Description

Limits how many output lines EPIC receives from a process managed by [exec](../commands/exec.md). This can prevent accidentally launched commands from producing excessive output. The count combines standard output and standard error for that process and is measured in received lines, not wrapped screen rows or bytes.

After processing a line, EPIC checks whether the count has reached the positive limit. If so, it reports `Ignoring process ... (reached output limit)` and closes the process's input and output pipes. It does not send the child a termination signal. The child may exit because its pipes were closed or may continue running; final cleanup and exit notification can occur later.

Lines count before callbacks, hooks, or redirection decide how to handle them, so hiding output with a hook does not avoid the limit. Partial buffered text that has not produced a line does not increment the counter through this path.

## Global and process-specific limits

The global value is read during process-output handling, so changing it can affect processes already running. Their received-line counts are not reset. Raising the limit does not reopen pipes already closed by an earlier limit.

A nonzero process-specific `exec -limit` value takes precedence over this global setting. A process-specific value of zero falls back to `shell_limit`; it does not force unlimited output when the global value is positive. The effective limit is enforced only when it is positive. This applies to exec processes using either a shell or direct execution.

The setting is an output-line limit, not a memory, execution-time, or operating-system resource limit. Setting it to zero disables the global cutoff but does not override nonzero per-process limits.

## Examples

Apply a global limit of 100 received lines per process:

```text
/set shell_limit 100
```

Give one new process a different limit:

```text
/exec -limit 20 some-command
```

Restore unlimited global output with `/set shell_limit 0`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): integer registration and zero default.
- [source/exec.c](../../source/exec.c): `handle_filedesc` counts received lines; `do_exec` selects the effective limit and checks it after handling output; `ignore_process` closes pipes without signalling the child; exec option parsing assigns process-specific limits.
