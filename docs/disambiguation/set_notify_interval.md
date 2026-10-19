# set notify_interval

## Removed setting

The former setting used this syntax:

```text
/set notify_interval <seconds>
```

It determined how often EPIC sent the built-in notify list to servers for checking. A value of zero suspended that polling without clearing the list.

EPIC6 retires the built-in notify polling system and does not register `notify_interval` as a setting. There is no supported built-in interval, default, or zero-to-suspend behavior under this name. Without script-provided handling, `/set notify_interval 60` reports `No such variable "NOTIFY_INTERVAL"`. Remove obsolete assignments from startup scripts.

## Current notify behavior

The [notify](../commands/notify.md) alias in [script/notify](../../script/notify), loaded by `builtins`, uses server MONITOR requests. It does not read `notify_interval` or run a periodic client polling timer. The server controls status notification timing and limits, and the alias reports when MONITOR support is unavailable.

With that script loaded and a supporting server, `/notify` requests current status, `/notify -alice` removes Alice from the watch list, and `/notify -` clears the server's list. Setting an interval to zero does not suspend MONITOR notifications. See the removed [set notify](set_notify.md) setting for the distinction between former polling and current script behavior.

## Older optional script

The separate [script/notify.jm](../../script/notify.jm) contains its own `on set` handler for `notify_interval` and initializes a script variable to `30` seconds. That handler accepts numeric values of at least one and recreates its `notcheck` timer when the value changes. It explicitly rejects zero; it does not implement the former zero-to-suspend rule. This is script-specific behavior, not a registered C setting or behavior of the current MONITOR wrapper. The interval handler also recreates the timer without checking that script's `notify` toggle.

The presence of this older script does not restore the retired built-in notify system or establish compatibility of the whole script with EPIC6.

## Source references

- [source/vars.c](../../source/vars.c): current setting registration omits this name and handles unknown-variable errors.
- [source/clock.c](../../source/clock.c): current system timers contain no built-in notify polling timer.
- [script/notify](../../script/notify) and [script/builtins](../../script/builtins): current MONITOR wrapper and its loading.
- [script/notify.jm](../../script/notify.jm): separate script variable, validation, and timer handler.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of built-in notify and its replacement by the MONITOR script.
