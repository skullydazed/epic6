# on exec_prompt

## Synopsis

```text
/on [<noise>]exec_prompt "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Registered as the hook for partial process output, but not emitted by the current [exec](../commands/exec.md) reader.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

The current implementation does not emit event data for this hook.

## Behavior

The reader uses line buffering and returns immediately on an incomplete line. The branch that would select `exec_prompt` is disabled at compile time. Consequently, output without a newline does not currently trigger this hook. Complete stdout and stderr lines use [on exec](exec.md) and [on exec_errors](exec_errors.md), respectively.

Registering a handler is valid, but has no effect on the current reader. No active parameter layout or suppression behavior is available to rely on.

## Example

```text
on -exec_prompt "*" {
    echo Partial process output: $*;
};
```

## Source references

- [source/exec.c](../../source/exec.c): `do_exec` passes the event identifier to `handle_filedesc`, whose partial-line dispatch branch is disabled.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
