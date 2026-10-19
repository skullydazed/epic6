# set status_cpu_saver

## Removed setting

The former setting used this syntax:

```text
/set status_cpu_saver [<text>]
```

It selected the status-bar text displayed while CPU Saver mode was active. The field was represented by `%L` in [status_format](../set/status_format.md). It was a CPU Saver indicator, not a scrollback indicator.

EPIC6 removes CPU Saver mode, does not register `status_cpu_saver` as a setting, and has no `%L` entry in its status-expando table. There is no supported marker text, default, or CPU Saver state to display. The old identifier remaining in a header does not provide an active setting.

Without script-provided handling of unknown settings, `/set status_cpu_saver [CPU]` reports `No such variable "STATUS_CPU_SAVER"`. Remove obsolete assignments and `%L` fields from startup status formats. The current status compiler consumes an unrecognized percent field without inserting a replacement, so retaining `%L` does not restore the indicator.

A script could supply its own status text, but accepting the former setting name would not restore the removed scheduling mode. Related [cpu_saver_after](set_cpu_saver_after.md) and [cpu_saver_every](set_cpu_saver_every.md) settings are retired too. Current clock updates use [clock_interval](../set/clock_interval.md) directly without a CPU Saver override.

## History

`set status_cpu_saver` first appeared in EPIC4pre1.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of CPU Saver mode.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [include/vars.h](../../include/vars.h): retains its historical identifier without registration.
- [source/status.c](../../source/status.c): current expando table omits `%L`; status compilation supplies no replacement for unknown fields.
- [source/clock.c](../../source/clock.c): current clock timer uses `clock_interval` without CPU Saver scheduling.
