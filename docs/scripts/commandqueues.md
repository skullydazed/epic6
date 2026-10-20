# commandqueues

## Synopsis

```text
/load commandqueues
/1cmd <seconds>[,<reset>] <command>
/qcmd <queue> <command>
/fqcmd <queue> <command>
/q1cmd <seconds>[,<reset>] <queue>[,<queue>...] <command>
/fq1cmd <seconds>[,<reset>] <queue>[,<queue>...] <command>
/qcmd [<queue>]
/fqcmd [<queue>]
/timer.ue <delay> <command>
/scmd <server>[,<server>...] <command>
```

## Description

This script provides aliases for delaying, deduplicating, and queueing commands while avoiding an extra expansion of their arguments before execution. To reduce [quoting problems](../other/quoting_hell.md), put code in an [alias](../commands/alias.md) and pass its name to these commands. Expansion already performed by the calling alias or hook still applies; these helpers do not undo it. Explicit `eval` inside the command deliberately adds expansion.

The script uses `encode`, `decode`, and `servernum` from [builtins](builtins.md), normally loaded by the default startup script. Load `builtins` first if your configuration does not provide them. The queues and execution-history variables are in memory, not saved across restarts.

## Once-per-interval commands

`1cmd` executes the supplied command unless its history for the current server prevents it. The history key is the lowercase command text: differences in arguments matter, while differences only in letter case share a key. Commands without a server context use separate history. Time comparisons use whole seconds.

With a nonzero `<seconds>`, a command is permitted when at least that many seconds have elapsed since its stored timestamp. Suppressed calls are discarded, not scheduled for later. A zero interval executes immediately without maintaining that history.

The optional comma-separated `<reset>` implements a sliding quiet period: when another call arrives within that many seconds of the stored timestamp, it suppresses execution and moves the timestamp to now. Repeated calls within the reset interval can therefore postpone eligibility indefinitely. Without `<reset>`, its numeric value is zero. Use nonnegative integer durations; the script does not validate them or provide argument diagnostics.

## Queued commands

`qcmd` appends a command to a named queue for the current server; `fqcmd` prepends it. Within a queue, commands are removed from the front. Enqueueing requests a timer to drain queues after five seconds. Normal automatic draining takes one command at a time and schedules another pass five seconds later; the delay is not a server acknowledgement or a guarantee that the command succeeded.

Queues share a timer named `qcmd.<server-refnum>` and automatic selection prefers the first queue in case-insensitive name order. Use simple queue names without spaces or dots. Commands for different servers have separate queues; execution still relies on the timer's saved server/window context. Server disconnections, removed windows, and ordinary timer rules can affect execution.

With only a queue argument, `qcmd` or `fqcmd` immediately removes and executes one command from that queue. With no arguments, it attempts to choose a queue automatically. Manual draining can bypass the five-second spacing.

Called as functions, `$qcmd(<queue>)` and `$fqcmd(<queue>)` remove and return the next command string instead of executing it; they do not schedule the follow-up timer. An empty queue returns an empty string. Calling these functions with a queue and command enqueues the command but does not start its timer.

`q1cmd` and `fq1cmd` first look for the queued command in every named queue using case-insensitive whole-command comparison. If found, they do nothing. Otherwise they apply `1cmd` to the enqueue operation and put the command on the first named queue, at its end or beginning respectively. The comma-separated queue list is searched for duplicates, not used to distribute commands among queues.

### Scheduling limitations

The implementation is not a general-purpose flood-control guarantee:

- Enqueueing while the shared timer already exists attempts to create the same timer again; that attempt is rejected by the C timer implementation, leaving the existing timer in place.
- `islagged()` is an undefined placeholder in the supplied script. There is no built-in lag measurement behind it, and the current branch structure does not provide a reliable pause-and-resume mechanism even if a function of that name is supplied.
- The code uses double-quoted word storage and performs additional quote unescaping when returning or executing commands. It does not provide an arbitrary-code serialization guarantee. Prefer simple alias names over deeply quoted command bodies.

## Unexpanded timers and server context

`timer.ue` encodes command text before passing it to [timer](../commands/timer.md) and decodes it when the timer fires. This avoids the timer's additional expansion of the original command. The wrapper tries to pass leading timer options through, but uses a heuristic parser rather than the full timer grammar. Use simple delay-and-command forms; numeric command names and options with arguments can be misparsed. For complex timer options, use the built-in timer directly with appropriate quoting.

`scmd` runs the command for each comma-separated numeric server reference through [xeval](../commands/xeval.md), selecting that server's current window where available. Supply valid connected server references: `xeval` ignores invalid context selectors rather than guaranteeing a failure before running the command. The requested context is temporary, and this alias does not connect servers.

Queued network commands remain subject to connection state, server permissions, rate limits, and delivery failures. These helpers do not retry rejected IRC requests.

## Examples

```text
/load commandqueues
/alias queue_demo { echo Queued command ran; }
/qcmd normal queue_demo
/1cmd 30 queue_demo
/timer.ue 5 queue_demo
```

The first helper queues the alias for a later pass. The second executes it at most once per 30-second history interval. The timer invokes it after five seconds without another expansion of the alias name. Inspect or manually consume a queued command as text with `$qcmd(normal)` when writing your own queue-management alias.

## History

Written by CrazyEddy; first included in EPIC4-1.1.3.

## Source references

- [script/commandqueues](../../script/commandqueues): aliases, command storage, history, duplicate tests, and timer scheduling.
- [source/functions.c](../../source/functions.c): `push`, `unshift`, `shift`, `serverwin`, and function dispatch.
- [source/alias.c](../../source/alias.c) and [source/if.c](../../source/if.c): variable matching and ordered structure traversal used to select queues.
- [source/timer.c](../../source/timer.c): duplicate timer references, updates, delay, and saved execution context.
- [source/commands.c](../../source/commands.c): `xeval` context selection and restoration.
