# on send_exec

## Synopsis

```text
/on [<noise>]send_exec "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC attempts to send text to a process through `text_to_process` with local echo requested.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Process descriptor: `%<logical-name>`, or `%<exec-refnum>` if unnamed. |
| `$1` | Numeric exec refnum, without `%`; not the operating-system PID. |
| `$2-` | Text supplied to the process writer, before that writer adds a newline and performs outgoing recoding. |

## Behavior

EPIC attempts the write before invoking this hook. It can run even after the write reports failure, so it does not confirm delivery. Invalid, unlaunched, or closed-input processes return before the hook. Calls without local echo do not trigger it.

If default handling is allowed, EPIC displays the supplied text. A suppressing serial-zero handler replaces that local echo; it cannot cancel the write. This hook has nonrecursion protection. See [exec](../commands/exec.md) for process management.

## Example

```text
on ^send_exec "*" {
    echo [to process $0] $2-;
};
```

## Source references

- [source/exec.c](../../source/exec.c): `text_to_process` validates the process, writes recoded text, and gates local echo on this hook.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
