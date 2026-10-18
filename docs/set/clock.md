# set clock

## Synopsis

```text
/set clock [on|off|toggle]
```

Default: `on`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

Controls the client's regular clock updates and whether its standard status-clock field is displayed. With the clock on and [clock_interval](clock_interval.md) nonzero, EPIC refreshes its cached clock on that interval, normally 60 seconds. The cached value supplies `$Z` and the clock text used by the `%T` status field through [status_clock](status_clock.md).

Turning the clock off removes its regular system timer and makes the standard status-clock field empty. It does not clear `$Z`: that expando continues returning the cached clock, which can become stale. A zero `clock_interval` also disables regular updates even if `clock` remains on.

If you want a current `$Z` without a visible status clock, leave `clock` on and remove `%T` from the relevant [status_format](status_format.md), rather than disabling updates.

## Effects and interactions

Changing this setting requests status updates. Enabling it with a nonzero interval also schedules its next interval and refreshes the clock immediately. It does not create an independent wall-clock source for each window; the cached text is shared.

[Clock_format](clock_format.md), [clock_24hour](clock_24hour.md), and [metric_time](metric_time.md) control clock formatting or mode. Their update paths can recalculate the cached clock even when its regular timer is off, so `clock off` does not prohibit every clock recalculation.

In ordinary time, a recalculation that observes a changed local hour or minute invokes [on timer](../on/timer.md), followed by [on idle](../on/idle.md). Turning off regular updates therefore also stops their regular clock-driven delivery. This does not disable the separate [timer](../commands/timer.md) command or all other client timers. Metric mode uses a different milliday-based hook path.

The clock uses local system time. Delivery can be delayed by client event-loop work; it is not an exact alarm guarantee. Changing the setting sends no IRC request.

## Example

Enable ordinary clock updates at the default interval:

```text
/set clock_interval 60
/set clock on
```

To stop regular clock updates and hide the standard status-clock field:

```text
/set clock off
```

## Source references

- [source/clock.c](../../source/clock.c): `my_set_clock` updates the clock system timer and statuses; `update_system_timer` requires both enabled clock and nonzero interval; clock reset routines update cached text and invoke hooks.
- [source/status.c](../../source/status.c): `status_clock` returns an empty field when the setting is off.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and enabled default, with a 60-second default interval.
