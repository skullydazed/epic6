# on timer

## Synopsis

```text
/on [<noise>]timer "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by the client's clock update. A time or wildcard pattern selects which emitted clock values run your handler. This is a clock hook, separate from the [timer](../commands/timer.md) command for scheduling commands after a delay.

Use standard [on](../commands/on.md) matching and noise controls. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Current local time in `HH:MM` format with ordinary time enabled, or a milliday value with metric time enabled. |

Ordinary time always uses zero-padded 24-hour hours `00` through `23` and minutes `00` through `59`. Neither `clock_24hour` nor `clock_format` changes this hook argument; those settings change the displayed clock and `$Z`. Patterns containing an `am` or `pm` suffix do not match ordinary hook values.

## Timing and effects

With ordinary time enabled, EPIC invokes this hook when a clock update observes a changed local hour or minute. It then invokes [on idle](idle.md), in the primary server's context. Recalculating the clock several times within the same minute does not repeatedly invoke this hook.

The [clock](../set/clock.md) setting defaults to on and [clock_interval](../set/clock_interval.md) defaults to 60 seconds. Turning the clock off or setting a zero interval disables its regular system timer. Other operations, including changing clock formatting, can also recalculate the clock.

This is not an exact alarm scheduler. Event-loop delays or long clock intervals can skip matching times; missed minutes are not replayed. The comparison uses the observed hour and minute, not a date, so it is not a daily scheduling guarantee.

There is no built-in display or command for this event. Its suppression result is ignored and does not stop the following `idle` event or future clock updates. Commands executed by a handler provide any desired notification.

With [metric_time](../set/metric_time.md) enabled, EPIC instead emits the current milliday value formatted with at least three digits when that value changes. The `HH:MM` patterns below apply to ordinary time only.

## Example

Report the time once per observed hour, on the hour:

```text
on ^timer "%:00" {
    xecho -b The time is now $Z;
    beep;
};
```

The `%` wildcard matches the hour field. `$Z` uses your displayed clock format, which may differ from the hook's 24-hour argument.

## Source references

- [source/clock.c](../../source/clock.c): `reset_standard_clock` and `reset_metric_clock` generate hook arguments; system timer handling controls regular updates.
- [source/vars.c](../../source/vars.c): clock settings and their update callbacks.
- [include/config.h](../../include/config.h): default clock settings and disabled metric time.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
