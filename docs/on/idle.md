# on idle

## Synopsis

```text
/on [<noise>]idle "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by the client's clock update with its current local idle count. With ordinary time enabled, the count is whole minutes since EPIC last received user input from a screen. Input resets the timestamp before key processing, so you do not have to finish or submit a command. Script commands and incoming IRC messages do not themselves reset this timestamp.

Use the standard [on](../commands/on.md) matching and noise controls. The pattern matches the count; it is not a separate timer or threshold argument. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Whole minutes idle with ordinary time enabled; see the metric-time limitation below. |

The timestamp is initialized at client startup and shared across screens. In ordinary time, EPIC subtracts the timestamp's seconds from the current seconds and divides by 60. This is local client idleness, not the idle value reported by an IRC server. Servers commonly use activity such as sending messages to determine their own idle values.

## Timing and default behavior

In ordinary time, EPIC invokes `idle` after [on timer](timer.md) when a clock update observes a changed local hour or minute. It runs in the primary server's context. The clock is on by default with [clock_interval](../set/clock_interval.md) set to 60 seconds. Turning [clock](../set/clock.md) off or using a zero interval disables its regular system timer.

Clock scheduling and event-loop delays affect delivery. This is not an exact notification at the instant an idle threshold is crossed: values can be skipped if clock updates are delayed or farther apart. Changing clock formatting can also update the clock. A handler matching `5` runs when an emitted count is exactly `5`, not for every count above five.

There is no default action or automatic away command. Suppression does not reset idleness or stop later clock events. Any [away](../commands/away.md) request in a handler is script-provided behavior and depends on an IRC connection and server handling.

## Example

Request away status when the ordinary idle count is five minutes:

```text
on ^idle "5" {
    away I'm not paying attention again...;
};
```

This does not automatically clear away status when input resumes, and it can run again after a later idle period reaches the same count.

## Metric-time limitation

With [metric_time](../set/metric_time.md) enabled, a different clock branch emits a milliday-based value rather than elapsed minutes. The current calculation subtracts the current milliday value from the idle timestamp's milliday value, reversing the usual elapsed-time direction. Do not use the five-minute example or interpret `$0` as ordinary idle minutes in that mode.

## Source references

- [source/clock.c](../../source/clock.c): `reset_standard_clock` and `reset_metric_clock` calculate arguments and invoke the hook; system timer handling uses `clock` and `clock_interval`.
- [source/screen.c](../../source/screen.c): screen input handling resets `idle_time` before processing input.
- [source/irc.c](../../source/irc.c): initializes the shared idle timestamp at startup.
- [include/config.h](../../include/config.h): defines the default enabled clock, 60-second interval, and disabled metric time.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
