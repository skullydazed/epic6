# set cpu_saver_every

## Removed setting

The former setting used this syntax:

```text
/set cpu_saver_every [<seconds>]
```

It selected a slower interval for periodic clock, status, and notification work while CPU Saver mode was active. It was used together with [cpu_saver_after](set_cpu_saver_after.md), which controlled automatic activation after user idleness.

EPIC6 removes CPU Saver mode and does not register `cpu_saver_every` as a built-in setting. There is no supported interval, default, or zero-value activation rule for it now. The historical identifier left in a header does not provide an active setting.

Without script-provided handling of unknown settings, `/set cpu_saver_every 120` follows the ordinary unknown-variable path and reports `No such variable "CPU_SAVER_EVERY"`. Remove obsolete assignments from startup scripts. A script accepting that name would implement its own behavior, not restore the former built-in scheduling mode.

## Current controls

The built-in clock timer uses [clock_interval](../set/clock_interval.md) directly when [clock](../set/clock.md) is enabled. There is no CPU-saver override. For example:

```text
/set clock_interval 120
```

This selects a fixed clock refresh interval, rather than an interval activated only after idleness. It does not control all status updates or script-provided notification scheduling. Setting the clock interval to zero disables its regular timer, not a CPU Saver mode.

See the removed [cpu_saver binding](bind_cpu_saver.md) for obsolete manual activation bindings.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records the sunset of CPU Saver mode.
- [source/vars.c](../../source/vars.c): current registration omits the CPU Saver settings and handles unknown-variable errors.
- [source/clock.c](../../source/clock.c): system clock scheduling reads `clock_interval` without a CPU-saver interval.
- [include/vars.h](../../include/vars.h): retains historical identifiers without active registration.
