# timer

## Synopsis

```text
/timer
/timer -list
/timer [flags] <seconds> <commands>
/timer -update -refnum <name> [flags] [<seconds> <commands>]
/timer -delete <name|all>
/timer -delete_for_window <numeric-window-refnum>
```

## Description

`timer` schedules commands to run later and returns immediately. EPIC continues processing events while the timer is pending. If the client is busy when the deadline passes, execution waits until timer processing resumes; there is no exact-time guarantee. Use [pause](pause.md) when the calling script itself must remain suspended.

Each timer has a unique, case-insensitive reference name. `-refnum` chooses one; otherwise EPIC assigns an unused numeric name beginning at zero. A bare `timer` or `timer -list` displays pending command timers with their names, remaining seconds, remaining event counts, and commands. Internal callback timers are omitted from that listing.

The first non-flag word is the delay in seconds, converted with `atof` without validation. Decimal delays work; a nonnumeric word becomes zero. Use a finite nonnegative interval. Zero schedules an already-due timer for later event-loop processing, not execution inside the `timer` command. A leading negative delay is parsed as a flag and normally rejected. The rest of the text is saved as commands; a braced block is useful for several commands, but one unbraced command is also accepted. An empty command is permitted.

## Options

Flags precede the delay. Names are case-insensitive and accept prefixes; `-ref` selects `-refnum`, while `-r` selects `-refnum` before `-repeat`. The parser also accepts `/` instead of `-` before a flag. Prefer the full names below.

| Flag | Effect |
| --- | --- |
| `-list` | Display pending command timers and stop processing this invocation. |
| `-refnum name` | Choose the timer's name. A duplicate name is an error unless `-update` is used. |
| `-repeat count` | Execute that many times in total; the default for a new timer is 1. `-1` or `*` repeats indefinitely; 0 makes the entire invocation a no-op. |
| `-update` | Update the named timer, or create it when the name is unused and a delay is supplied. |
| `-window description` | Bind execution to an existing window. |
| `-server description` | Bind execution to an existing server-list entry. |
| `-general` | Select execution based on the current window/server at firing time. |
| `-cancelable` | Skip execution when the bound window or server entry no longer exists. |
| `-snap` | Align the initial deadline using a whole-second interval calculation; see timing below. |
| `-delete name` | Delete a pending timer and stop processing this invocation. |
| `-delete all` | Delete all command timers, retaining internal callback timers. |
| `-delete_for_window refnum` | Delete command timers explicitly bound to that numeric window refnum. |

Repeat counts use base-detecting integer conversion rather than strict validation. Use ordinary positive integers, `-1`, or `*`; other negative values do not provide a useful finite count. Indefinite timers with an explicitly supplied interval below 0.01 seconds are rejected.

Deletion first checks whether the requested name exists. A timer literally named `all` is deleted individually; only when no such timer exists does `all` select bulk deletion. Bulk deletion does not affect server or general timers merely because they might execute in that window. Missing flag arguments, unknown flags, unavailable windows or servers, and nonexistent timer references produce diagnostics.

## Server, window, and general timers

Timers bind to one of three domains:

- **Server timers:** created by default while EPIC is parsing server data, or with `-server`. At execution, EPIC selects that server and its current window. `-server -1` selects the current server context. The entry need not be connected when the timer fires.
- **Window timers:** the default outside server parsing, or with `-window`. At execution, EPIC selects that window and its current server association. A window may have changed servers since creation.
- **General timers:** selected with `-general`. They use the current window at firing time and normally set the server context to that window's server; do not assume it is the creation-time server.

The last domain flag wins. `-window` accepts the window lookup forms; `-server` accepts existing server references/descriptions as described by [server](server.md), and does not create a new server. `-window -1` allows an unspecified window reference; in an update it preserves the previous domain refnum, while a newly created timer has no valid bound window.

If a bound window or server entry is gone, a noncancelable timer still executes using the available current context. A cancelable timer skips that occurrence. For a repeating timer, the next occurrence is scheduled **before** this check, so skipping does not delete the remaining repetitions. Server cancellation tests the existence of the server entry, not a lost connection. Refnums can be reused, so binding is not a guarantee about the original object's identity. After execution, EPIC attempts to restore the previously current window and server context.

## Execution, repetition, and updates

Saved commands execute later as a `TIMER` callback, with a fresh named local scope and the creation-time positional argument text. Variables in the saved body are resolved when it executes; the timer does not preserve the creator's local variables. Normal enclosing script expansion can still occur before the handler receives its arguments, so keep delayed expansions inside the body.

Timers run in scheduled-deadline order, with equal deadlines retaining insertion order. Repetitions advance from the previous scheduled deadline, rather than from completion of the commands. A delayed repeating timer can therefore run overdue occurrences close together. The next repeat is already pending while the current body runs, allowing that body to delete or update its own timer. A one-shot timer is removed from the pending list before its body runs.

`-snap` affects initial scheduling only. Its calculation converts the interval to an integer number of seconds; intervals below one second can cause division by zero. Use a positive whole-second interval. The calculation chooses an epoch-based interval boundary and can skip the immediately following boundary for intervals greater than one second. Subsequent repeats advance by the original interval, rather than recalculating snapped deadlines.

Updating requires `-refnum`. An existing timer retains its interval and current deadline when no delay is supplied, and retains its repeat count when `-repeat` is omitted. A supplied delay resets its deadline relative to the update time, or through `-snap`.

However, an update always replaces the command text and positional argument text, reselects the domain from the current invocation's flags/default, and sets cancelability from that invocation. With no commands, the old body is replaced by an empty body. Restate the commands, domain, and `-cancelable` when needed; an update is not a general patch of only the mentioned flags. A missing timer can be created by `-update` only when a valid explicit reference and delay are supplied.

New timers created while loading a package receive its [package](package.md) label; updating an existing timer retains its previous label. The callback does not reactivate the creation-time load/package context. [timerctl](../functions/timerctl.md) provides additional inspection and control. The command has no script return value or normal creation confirmation and requires no IRC connection by itself.

## Examples

```text
timer -refnum reminder 600 {beep; echo Reminder: ten minutes have passed}
timer -general 15 {echo Fifteen-second reminder}
timer -refnum periodic -repeat 3 10 {echo Another scheduled check}
timer -update -refnum reminder 300 {beep; echo Reminder moved to five minutes}
timer -delete periodic
timer -delete all
```

These schedule a reminder, a general timer, and three executions spaced ten seconds apart, then update and delete pending command timers.

## Source

Registration is in [source/commands.c](../../source/commands.c). `timercmd()`, scheduling, cloning, deletion, package tagging, and execution are in [source/timer.c](../../source/timer.c). Callback scope and execution are in [source/commands.c](../../source/commands.c), while interval conversion and snap calculations are in [source/ircaux.c](../../source/ircaux.c). Server and window validity and selection are in [source/server.c](../../source/server.c) and [source/window.c](../../source/window.c).
