# set notify_on_termination

## Synopsis

```text
/set notify_on_termination [on|off|toggle]
```

Default: `on`. With no argument, [set](../commands/set.md) displays the current value.

## Description

When on, displays a built-in message when EPIC cleans up a process managed by [exec](../commands/exec.md). The message identifies its exec refnum and command, followed by its return code or terminating signal name and number. A separate administrative cleanup path can report that the process was disowned.

When off, suppresses these built-in termination messages. It does not suppress process output, stop cleanup, or prevent [on exec_exit](../on/exec_exit.md) from running. This is a global setting checked at cleanup time, so changing it also affects processes already running.

## Hooks and callbacks

The built-in message appears only when `exec_exit` permits default handling. Merely installing a hook does not necessarily suppress it; hook noise and suppression rules determine that. For example, a suppressing `^exec_exit` hook can replace the default message with its own output regardless of this setting.

A nonempty process-specific `exec -exit { ... }` callback replaces both the hook and this default notification. Pending [wait](../commands/wait.md) process commands and `exec -end` callbacks run first and are not disabled by this setting.

Normally cleanup waits for both process termination and completion of its output I/O, so notification can occur later than the operating system's exit event. The message runs in the process's stored server and output-window context at the `other` level and remains subject to normal output handling. This setting does not provide a user-facing disown command or restart terminated processes.

## Example

Suppress default termination lines while retaining process output and exit handlers:

```text
/set notify_on_termination off
```

Restore them with `/set notify_on_termination on`. Exit callbacks or suppressing hooks can still replace them.

## History

This setting first appeared in ircII.

## Source references

- [source/exec.c](../../source/exec.c): `cleanup_dead_processes` checks cleanup readiness, runs wait commands and exit handlers, and conditionally displays termination information; `set_context_for_process` selects its output context.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and on default.
