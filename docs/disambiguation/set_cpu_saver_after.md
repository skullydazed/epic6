# set cpu_saver_after

## Removed setting

The former setting used this syntax:

```text
/set cpu_saver_after [<minutes>]
```

It selected how many minutes without a keypress would activate CPU Saver mode. That mode reduced periodic work while the user was idle, in conjunction with `cpu_saver_every`; a zero value disabled automatic activation.

EPIC6 removes CPU Saver mode and does not register `cpu_saver_after` or `cpu_saver_every` as built-in settings. There is no supported delay, default, or automatic transition controlled by this name now. Stale identifiers in headers do not restore the feature.

Without script-provided handling of unknown settings, `/set cpu_saver_after 10` follows the ordinary unknown-variable path and reports `No such variable "CPU_SAVER_AFTER"`. Remove obsolete assignments from startup scripts. A script accepting the same name would supply its own behavior, not enable a built-in CPU Saver mode.

## Current behavior

The built-in clock uses [clock](../set/clock.md) and [clock_interval](../set/clock_interval.md) directly, without an idle CPU-saver override. Status updates are not suspended by this removed setting. Any notification checks supplied by scripts follow those scripts' scheduling rules.

For a fixed, less frequent clock refresh, configure its actual interval explicitly:

```text
/set clock_interval 120
```

This applies immediately when the clock is enabled; it does not wait for ten minutes of idleness or change unrelated script timers. The former [cpu_saver binding](bind_cpu_saver.md) is also removed.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records the sunset of CPU Saver mode.
- [source/vars.c](../../source/vars.c): current variable registration omits both CPU Saver settings; unknown variables follow the ordinary error-hook path.
- [source/clock.c](../../source/clock.c): clock system scheduling uses `clock` and `clock_interval`, with no CPU-saver transition.
- [include/vars.h](../../include/vars.h): retains historical identifiers without an active variable registration.
