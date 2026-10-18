# set clock_format

## Synopsis

```text
/set clock_format [<text>]
/set -clock_format
```

Default: unset. With no text, [set](../commands/set.md) displays the current value. `/set -clock_format` unsets it and restores selection of the built-in format. The format text can contain spaces.

## Description

EPIC uses the system's `strftime` function to construct its ordinary local clock, available as `$Z` and through the status bar's `%T` field. When this setting is unset, the format is `%R` if [clock_24hour](clock_24hour.md) is on, or `%I:%M%p` if it is off.

When set, this string is used as the `strftime` format and `clock_24hour` is disregarded. This is a time-format string, not an EPIC status-format string: for example, `%H:%M:%S` means hours, minutes, and seconds here. Platform `strftime` support and the active locale determine the available conversions and localized names.

The ordinary clock uses local system time. [Metric_time](metric_time.md) selects a separate clock path that ignores this setting. Changing the format does not change the system timezone, server time, or the format of [on timer](../on/timer.md), which still receives ordinary 24-hour `HH:MM` values.

## Updates and limits

Setting or unsetting the format recalculates the cached clock immediately, even when its regular timer is disabled. It requests status updates when [status_clock](status_clock.md) is nonempty. A recalculation that observes a new hour or minute can also invoke `timer` and [idle](../on/idle.md).

Regular updates require [clock](clock.md) on and a nonzero [clock_interval](clock_interval.md). The default interval is 60 seconds, so including seconds in the format is useful only if you shorten the interval. This path has no CPU-saver interval adjustment. Event-loop delays can still postpone an update.

The clock output buffer is 256 bytes, including its terminating null. EPIC does not validate the format or report `strftime` failure; keep the result comfortably within that buffer and use conversions supported on your platform. A malformed or oversized format has no guaranteed useful result.

Changing this setting affects future clock text, not existing scrollback, and sends no IRC request.

## Example

Show a local 24-hour clock with seconds:

```text
/set metric_time off
/set clock_format %H:%M:%S
/set clock_interval 1
/set clock on
```

Restore the built-in format and default update interval:

```text
/set -clock_format
/set clock_interval 60
```

## History

This setting first appeared in EPIC4pre1.019.

## Source references

- [source/clock.c](../../source/clock.c): `reset_standard_clock` applies `strftime`; `set_clock_format` refreshes immediately; the clock system timer controls periodic updates.
- [source/status.c](../../source/status.c) and [source/functions.c](../../source/functions.c): the status clock and `$Z` use cached clock text.
- [source/vars.c](../../source/vars.c): string registration, unset handling, and refresh callback.
- [include/config.h](../../include/config.h): unset format and 60-second interval defaults.
