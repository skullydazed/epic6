# utime

Return the current wall-clock time as seconds and microseconds.

## Synopsis

```irc
$utime()
```

## Technical

Like [time](time.md), this function reports seconds since the Unix epoch, 1970-01-01 00:00:00 UTC. It also supplies the microseconds within that second. It takes no meaningful arguments; supplied text is ignored.

Each call reads `CLOCK_REALTIME` directly through `get_time`, rather than returning EPIC's cached event-loop time. The clock's nanosecond field is divided by 1000, discarding the remaining nanoseconds. The microsecond field is not padded with leading zeroes.

## Practical

Use [stime](stime.md) or [strftime](strftime.md) to format the seconds field for human reading. For arithmetic, interpret the two fields as seconds plus microseconds divided by one million. Do not simply join them with a decimal point unless the second field is padded to six digits.

The output has microsecond resolution, but actual clock precision and accuracy depend on the system. This is a wall clock: clock corrections can move it forwards or backwards, so it does not guarantee increasing values or steady elapsed-time measurements.

## Returns

Two space-separated decimal integers: epoch seconds and microseconds within that second, normally from `0` through `999999`. These are integer fields, not a floating-point result; floating-point display settings do not control their formatting. The implementation does not check `clock_gettime` failure, so it supplies no reliable error result if that call fails.

## Examples

```irc
$time()  /* Might return 850381369. */
$utime() /* Might return 850381369 56367, representing 850381369.056367. */
```

## Source

Registered and implemented as `function_utime` in [source/functions.c](../../source/functions.c). `get_time` reads `CLOCK_REALTIME` in [source/ircaux.c](../../source/ircaux.c).
