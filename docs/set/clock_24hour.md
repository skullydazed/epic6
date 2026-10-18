# set clock_24hour

## Synopsis

```text
/set clock_24hour [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

When on, EPIC's ordinary clock uses 24-hour time, such as `21:05`. When off, it uses 12-hour time with a leading zero and an AM/PM designation, such as `09:05PM`. The latter designation comes from the system's `strftime` `%p` conversion and can depend on the active locale.

The cached clock is available to scripts through `$Z` and to the status bar's `%T` field through [status_clock](status_clock.md). The [clock](clock.md) setting controls regular updates and whether the standard status-clock field is shown.

## Precedence and effects

A configured [clock_format](clock_format.md) takes precedence over this setting. Unset that string with `/set -clock_format` to restore selection between the two built-in formats. With [metric_time](metric_time.md) on, the metric clock path ignores both ordinary formats.

Changing `clock_24hour` immediately recalculates the clock, even when its regular timer is disabled. It requests status updates when `status_clock` is nonempty. It does not change the system timezone or server time, and sends no IRC request.

This is a display-format setting, not the format selector for [on timer](../on/timer.md). In ordinary time that hook always receives zero-padded 24-hour `HH:MM`, independent of this value. A recalculation can also invoke `timer` and [idle](../on/idle.md) if it observes a new hour or minute.

## Example

Select the ordinary 24-hour clock:

```text
/set metric_time off
/set -clock_format
/set clock_24hour on
```

Use `/set clock_24hour off` for the ordinary 12-hour clock. Leave `clock` on for regular updates to `$Z`.

## Source references

- [source/clock.c](../../source/clock.c): `reset_standard_clock` selects custom format, `%R`, or `%I:%M%p` in that order; `reset_clock` chooses ordinary or metric mode and requests status updates.
- [source/status.c](../../source/status.c): `status_clock` uses the cached clock for the status field.
- [source/functions.c](../../source/functions.c): `$Z` returns `get_clock()`.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration, reset callback, and default off.
