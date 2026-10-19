# set metric_time

## Synopsis

```text
/set metric_time [on|off|toggle]
```

Default: `off`. With no argument, [set](../commands/set.md) displays the current value.

## Description

Enables metric time for EPIC's clock display and its clock-driven hooks. Metric time uses the day as its unit: one milliday is one thousandth of a day, or 86.4 seconds. Time is measured from midnight UTC, with no local time-zone adjustment. For example, UTC noon is milliday `500`.

With this setting on, the status clock and `$Z` show the integer milliday, from `0` through `999`. The status clock does not pad the number with leading zeros. [clock_format](clock_format.md) and [clock_24hour](clock_24hour.md) apply to the standard clock, not this metric display. Turning metric time off restores standard clock formatting.

Changing this setting refreshes the clock immediately. Subsequent automatic refreshes still depend on [clock](clock.md) and [clock_interval](clock_interval.md); the interval remains measured in seconds. Refreshes are not automatically scheduled at milliday boundaries, so the display can skip values if updates are infrequent.

## Hook arguments

On a refresh that observes a different integer milliday from the previous metric refresh, EPIC runs [on timer](../on/timer.md) with a three-digit milliday such as `005` or `500`. It then runs [on idle](../on/idle.md) in the primary server context.

The metric `on idle` argument has a limitation: the implementation subtracts the current integer milliday from the integer milliday of the last input timestamp. It therefore normally produces a negative value as inactivity increases, and wraps incorrectly across UTC midnight. Do not interpret it as a reliable positive elapsed-idle duration. This differs from the standard clock's elapsed whole-minute argument.

Metric time does not change all internal time processing. [timer](../commands/timer.md), [pause](../commands/pause.md), and [sleep](../commands/sleep.md) retain their normal time units; this setting does not convert their intervals to millidays.

## Example

```text
/set metric_time on
/set clock on
```

Return to the standard clock with `/set metric_time off`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration, clock-reset callback, and off default.
- [source/clock.c](../../source/clock.c): `reset_metric_clock`, clock-format selection, hook arguments, and second-based system timer.
- [source/ircaux.c](../../source/ircaux.c): `timespec_to_metric` and `get_metric_time` compute millidays within the UTC day.
- [source/status.c](../../source/status.c): status clock uses the cached clock text.
