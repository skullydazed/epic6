# $killpid()

## Synopsis

`$killpid(<signal> [<pid> ...])`

## Technical

Sends the selected operating-system signal to each supplied process ID, without invoking an external `kill` command. The signal is required; there is no default. A valid signal with no process IDs returns `0` without sending anything.

Signals can be numeric or EPIC signal names, matched case-insensitively and exactly. Named signals normally omit the `SIG` prefix, for example `term`, `hup`, or `kill`. EPIC uses `SIG<number>` fallback names for other signal slots. Available names and signal numbers depend on the platform.

A signal word containing decimal digits, optionally preceded by `-`, takes the numeric path. Conversion then uses base detection; leading-zero octal is therefore significant. Only numbers from `1` through `127` pass EPIC's range check. Signal `0` is rejected, so this is not a process-existence or permission-check function. An in-range number can still be unsupported by the operating system.

Process IDs are converted with `strtoul` in base `10`, then assigned to `pid_t`. Conversion is not validated: numeric prefixes are accepted, nonnumeric text becomes zero, and negative or oversized values undergo platform-dependent conversion to `pid_t`. Supply valid decimal IDs within the platform's range.

The resulting ID is passed directly to `kill`. A positive ID selects one process. Zero selects the caller's process group; negative IDs can select process groups or, for `-1`, a broad set of processes, subject to platform rules and permissions. EPIC does not filter these targets. See the [POSIX kill specification](https://pubs.opengroup.org/onlinepubs/009604499/functions/kill.html).

## Returns

The count of successful `kill` calls, not necessarily the number of distinct processes signaled. Repeated IDs are attempted repeatedly, and one group-targeting call can affect many processes. Failed calls contribute nothing to the count; processing continues, and individual errors are not returned or diagnosed by this function.

Missing or unrecognized signals, and numeric signals outside EPIC's accepted range, return the empty string. Success means the operating system accepted the request, not that a process exited; signal handling depends on the signal and target.

## Practical

Use this when a script has a known process ID and needs to send it a specific signal. The operating system enforces permissions; the function provides no extra authorization or target validation.

## Examples

`$killpid(term <known-pid>)` sends the termination-request signal to that process and normally returns `1` on success or `0` on failure. Replace the placeholder with a validated decimal process ID.

`$killpid(term)` returns `0` without sending a signal. `$killpid(0)` returns the empty string because signal zero is not supported.

## History

This function was added in EPIC4-1.1.6.

## Source

Registered and implemented as `function_killpid` in [source/functions.c](../../source/functions.c). Signal-name tables, numeric classification, and conversion are in [source/ircaux.c](../../source/ircaux.c); `MY_SIG_MAX` is defined in [include/irc_std.h](../../include/irc_std.h).
