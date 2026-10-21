# Process signals relevant to EPIC

EPIC's behavior can be controlled to an extent from the Unix command line through process signals. These are operating-system signals sent to the client process, not IRC protocol messages.

## Notable signals

| Signal | Effect |
| --- | --- |
| `TERM` | Enters normal client exit handling, including exit hooks, server closure, logging and process cleanup, and terminal restoration. |
| `HUP` | Normally uses the same exit handler as `TERM`; ignored when the client starts in detached mode. |
| `KILL` | Operating system terminates the process without EPIC cleanup or script hooks. EPIC cannot catch it. |
| `USR1` | Prints a notification and cleans up tracked exec processes. Cleanup sends `TERM`, waits two seconds, reaps exited children, then sends `KILL` to remaining tracked processes. |
| `USR2` | Raises the client's system-exception flag, used to interrupt script execution paths that check it. It does not terminate the client by itself. |
| `INT` | Records an interrupt. Repeated interrupts before normal processing resets the counter raise a system exception; the fifth invokes normal client exit handling. |
| `WINCH` | Requests screen refresh after a terminal resize. |
| `CONT` | Invokes terminal continuation handling. |
| `SEGV`, `BUS` | Enter the critical-error handler. This reports the error and takes the crash or exit path described below. |
| `QUIT`, `PIPE` | Installed as ignored signals. |

`usr1` does not close DCC connections: its current built-in action is exec-process cleanup. It can block briefly while waiting for child processes. Normal exit is also more than an instantaneous termination; hooks and cleanup run before the process exits.

## Sending a signal

Find the client's process ID with `ps` or another process viewer, then use your shell's `kill` command. For example, from a Unix shell:

```sh
kill -USR1 <PID>
```

Replace `<PID>` with the actual process ID; the angle brackets are placeholders. This example terminates the client's tracked exec processes. Use `kill -TERM <PID>` to request client exit. Signal numbers and supported names vary by operating system, so prefer symbolic names and consult your system's manual pages for `kill` and signals. Delivery requires the appropriate operating-system permissions and a live target process.

## Script notifications

[on signal](../on/signal.md) observes signals recorded by EPIC's signal wrapper when the event loop later processes them. For each recorded occurrence, it invokes the hook once with the signal number and once with its symbolic name, each followed by the pending count. A catch-all handler can therefore run twice per occurrence.

The built-in C callback runs when the signal is caught, before the deferred script notification. Suppressing the hook does not undo its action or veto exit. Ignored/default signal handling bypasses recording, and a terminating or crashing signal can prevent script notification entirely. Operating-system coalescing also means the number of signals sent is not necessarily the number recorded.

## Critical errors and core dumps

Core dumping is not conditional on an `EPIC_DEBUG` compile option. The critical-error handler uses the runtime `xdebug crash` flag. With that flag clear it takes the normal cleanup/exit path after reporting the critical error. With the flag set it waits in a diagnostic loop while the flag remains set and sleeping succeeds. After that loop, it chooses the crash path only if the flag is still set; otherwise it uses normal exit handling. The crash path raises `ABRT` with default handling and also attempts `QUIT` as a fallback. An actual core file depends on operating-system limits and core-dump configuration. Sending `SEGV` deliberately is not a normal shutdown mechanism.

## Source references

- `source/irc.c`: startup signal assignments, individual callbacks, deferred signal hooks, critical-error handling, and exit cleanup.
- `source/ircsig.c`: signal registration, recording wrapper, and uncatchable-signal exclusions.
- `source/exec.c`: tracked-process termination and cleanup.
- `source/debug.c`: runtime `crash` debug flag.
