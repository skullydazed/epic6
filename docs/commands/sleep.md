# sleep

## Synopsis

```text
/sleep <seconds>
```

## Description

`sleep` performs a blocking sleep for the requested number of seconds. Decimal intervals such as `0.5` are supported. While blocked, EPIC does not process user input, server traffic, timers, or other event-loop work. The calling script resumes when the wait returns.

Data may accumulate while EPIC is blocked, and remote peers can disconnect or time out. Although the client does not process those changes during the sleep, conditions can have changed when it resumes. Do not use `sleep` to wait for a server response; use [wait](wait.md) or a suitable reply hook instead.

Use [pause](pause.md) to suspend the calling script while EPIC continues processing events. Use [timer](timer.md) to schedule a later command without keeping that script suspended.

## Arguments and timing

Only the first whitespace-delimited argument is used; additional text is ignored. There are no options or default interval. Omitting the argument prints `Usage: SLEEP <seconds>`.

The argument is converted with `atof`, without numeric validation, then stored with floating-point precision. A nonnumeric value becomes zero. The implementation requests a `poll()` timeout of `seconds * 1000 + 1`, converted to integer milliseconds, so zero requests about one millisecond and fractional timing is limited to millisecond resolution. Scheduling can make the actual delay longer.

Use a finite nonnegative interval within the platform's timeout range. Negative values can produce an indefinite wait or an error, depending on the converted timeout and platform. Very large or non-finite values have no useful supported timing behavior.

The command makes one blocking wait. A signal or system error can end it early; EPIC does not retry the remaining interval or report the wait's error. It produces no completion message or script return value and does not require an IRC connection.

## Examples

```text
sleep 0.5
echo continuing
```

In a script, this blocks the client for approximately half a second before executing the following `echo`. Use `pause 0.5` instead when the client should remain responsive.

## History and source

The command existed in ircII 2.1.5g. Subsecond support first appeared in EPIC4-1.1.2.

Registration and `sleepcmd()` are in [source/commands.c](../../source/commands.c). The blocking `my_sleep()` helper and its `poll()` call are in [source/newio.c](../../source/newio.c).
