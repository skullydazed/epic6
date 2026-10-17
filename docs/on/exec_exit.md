# on exec_exit

## Synopsis

```text
/on [<noise>]exec_exit "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC cleans up a process managed by [exec](../commands/exec.md), normally after it exits or is killed by a signal and both output streams have finished. The event can occur later than operating-system termination while output is still being read. It reports removal of EPIC's process record rather than each intermediate process-status change.

The hook runs regardless of [set notify_on_termination](../set/notify_on_termination.md), unless a nonempty process `-exit` callback replaces it. Use standard [on](../commands/on.md) matching and noise controls; there are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Process logical name, if assigned; otherwise its numeric exec refnum without `%`. |
| `$1` | Terminating signal number, normally `0` for a normal exit. |
| `$2` | Recorded exit-status field; use as an exit code only for a normal exit. |

The exec refnum is not the operating-system process ID. On signal termination, `$2` is not a meaningful normal exit code; check `$1` first. Signal numbering is platform-dependent. The pattern matches the identifier and both numeric fields together.

The former general claim that this event follows a user-requested disown is not supported by a current disown operation. An administrative kill of an unlaunched process marks its record for cleanup and can also reach this event without an operating-system child exit.

## Default behavior and ordering

Pending [wait](../commands/wait.md) process commands and `exec -end` callbacks run first, receiving the same three fields. A nonempty `exec -exit { ... }` callback then runs instead of this hook, also receiving all three fields. The process has already been removed from the lookup table before these callbacks or the hook run, so its old identifier is no longer available for ordinary process queries.

When the hook permits default handling and `notify_on_termination` is on (the default), EPIC displays a process termination line with its exec refnum, command text, and return code or signal name and number. The administrative cleanup path can instead display a disowned message. Turning the setting off suppresses those built-in lines without suppressing this hook. A suppressing hook, such as a serial-zero `^exec_exit` hook, also prevents them.

The event runs in the process's stored server and output-window context at the `other` message level. It does not itself restart the process or send an IRC request.

## Example

Display normal exit codes separately from signal termination:

```text
on ^exec_exit "*" (process, signal, code) {
    if (signal) {
        xecho -b Process $process terminated by signal $signal;
    } else {
        xecho -b Process $process exited with code $code;
    };
};
```

## History

This hook first appeared in ircII.

## Source references

- [source/exec.c](../../source/exec.c): `get_child_exit` records child status; `cleanup_dead_processes` waits for completed I/O, removes the record, runs callbacks and hooks, and controls notification; `kill_process` handles unlaunched records; `set_context_for_process` selects output context.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): register `notify_on_termination` and its default.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard matching and suppression.
