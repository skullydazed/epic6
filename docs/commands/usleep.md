# usleep command

## Synopsis

```text
/usleep microseconds
```

## Description

`usleep` suspends the client for an interval supplied in microseconds. During the blocking wait, EPIC does not process user input, server traffic, timers, or other event-loop work. The calling script resumes when the wait returns. This is mainly useful in occasional noninteractive scripts that deliberately need to block the entire client.

Only the first whitespace-delimited argument is used; additional text is ignored. There are no options or default interval. Omitting the argument prints `Usage: USLEEP <usec>`. The command produces no completion message or function return value and requires no IRC connection.

## Timing and restrictions

The argument is parsed as an integer with automatic base detection: ordinary decimal, leading-zero octal, and `0x` hexadecimal forms are accepted. Parsing stops at the first unsupported character without a diagnostic; `1000.5` therefore means 1000 microseconds, and a nonnumeric value means zero. Use plain decimal integers without unnecessary leading zeros.

Despite the microsecond input unit, the implementation converts to seconds and performs a `poll()` wait with an integer-millisecond timeout computed as `microseconds / 1000.0 + 1`. Zero requests about one millisecond, and timing is limited to millisecond resolution. Scheduling can make the actual delay longer. The wait is made once; a signal or system error can end it early, and EPIC does not retry the remaining interval or report the wait's error.

Use a nonnegative value that fits both the platform's integer parser and millisecond timeout range. Negative timeouts can produce an indefinite wait or an error, depending on the converted value and platform. Out-of-range inputs have no useful supported timing behavior.

Data can accumulate and remote connections can time out while the client is blocked. Use [`pause`](pause.md) when the script should wait while EPIC continues processing events, or [`timer`](timer.md) to schedule later work. Use [`wait`](wait.md) or a reply hook for server responses. [`sleep`](sleep.md) supplies a blocking interval in seconds instead.

## Example

```text
usleep 500000
echo continuing
```

In a script, this blocks the client for approximately half a second before the message is displayed. `pause 0.5` is the alternative when the client should remain responsive.

## Source

[source/commands.c](../../source/commands.c) registers `usleep` and implements `usleepcmd()`. [source/ircaux.c](../../source/ircaux.c) implements `my_atol()` using `strtol()` with base zero. [source/newio.c](../../source/newio.c) implements the blocking `my_sleep()` helper and its `poll()` timeout.
