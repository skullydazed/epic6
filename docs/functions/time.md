# time

## Synopsis

`$time()`

## Technical

Returns the current system time in whole seconds since the Unix epoch, 1970-01-01 00:00:00 UTC, by calling `time(NULL)` on each invocation. It does not use EPIC's cached event-loop time. All arguments are ignored.

The timestamp does not depend on the local timezone. Its accuracy depends on the host's clock; different hosts need not agree. For subsecond resolution, use [`utime`](utime.md).

## Practical

Convert the timestamp to readable local time using [`stime`](stime.md) or [`strftime`](strftime.md). Store a timestamp and subtract it from a later value to calculate elapsed seconds; [`tdiff`](tdiff.md) can format the difference.

The system clock can move backward or forward when adjusted, so this is not a monotonic elapsed-time clock. The [`time` command](../commands/time.md) instead sends a server request and does not supply this local timestamp.

## Returns

A decimal timestamp. The handler returns the system result directly, including `-1` if the system call fails; it does not check errors separately. Resolution and representable dates depend on the platform's `time_t`. There are no options, settings, or server requirements.

## Examples

```irc
$time()    Might return 850381369.
```

## History

First appeared in ircII.

## Source

Handler: [`source/functions.c`](../../source/functions.c:1551). Integer return formatting: [`include/functions.h`](../../include/functions.h:56).
