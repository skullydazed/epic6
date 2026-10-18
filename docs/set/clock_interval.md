# set clock_interval

## Synopsis

```text
/set clock_interval [<seconds>]
```

Default: `60`. With no value, [set](../commands/set.md) displays the current interval. The value must be a non-negative integer; negative or nonnumeric values are rejected. Zero disables the regular clock timer.

## Description

Controls how often EPIC refreshes its cached local clock, used by `$Z` and the status bar's `%T` field. Regular updates require [clock](clock.md) to be on as well as a nonzero interval. With the default interval, updates are aligned toward minute boundaries rather than scheduled simply 60 seconds after the setting was changed.

The scheduler uses the next-interval calculation shared with snap timers, based on system epoch seconds. It recalculates the next deadline on each callback, rather than replaying every missed interval. An interval of one schedules the next whole second. For larger intervals, the current calculation can skip an imminent boundary when called during the second immediately before it. Client event-loop delays can also postpone updates; this is not an exact alarm guarantee.

## Effects and interactions

Changing a nonzero interval while `clock` is on replaces the pending clock timer, schedules its next deadline, and refreshes the clock immediately. Setting zero, or changing the interval while `clock` is off, removes the regular timer without that immediate refresh. Other settings such as [clock_format](clock_format.md) can still recalculate the clock independently.

A shorter interval allows seconds in the display format to change more frequently, but does not make [on timer](../on/timer.md) and [on idle](../on/idle.md) fire every second. In ordinary time they run only when a recalculation observes a changed hour or minute. Longer intervals can skip clock-hook values. [Metric_time](metric_time.md) uses a separate milliday-based hook path.

The current clock path has no CPU-saver override. This setting affects the clock system timer, not every timer in EPIC or the delays specified with the [timer](../commands/timer.md) command. It sends no IRC request.

## Example

Display seconds and refresh near each whole second:

```text
/set metric_time off
/set clock_format %H:%M:%S
/set clock_interval 1
/set clock on
```

Restore the default refresh interval with `/set clock_interval 60`. Disable regular clock refreshes with `/set clock_interval 0`; `$Z` can then remain stale until another clock recalculation occurs.

## Source references

- [source/clock.c](../../source/clock.c): `set_clock_interval`, `update_system_timer`, and `system_timer` replace, disable, and schedule the clock timer, then call clock refresh.
- [source/ircaux.c](../../source/ircaux.c): `time_to_next_interval` computes boundary-based delays.
- [source/vars.c](../../source/vars.c): integer validation and setting callback.
- [include/config.h](../../include/config.h): default interval is 60 seconds and clock defaults to on.
