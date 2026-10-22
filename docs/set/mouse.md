# set mouse

## Synopsis

```text
/set mouse [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value.

## Description

Enables terminal mouse reporting and binds the SGR mouse-report prefix (`ESC [ <`) to EPIC’s mouse keymap. Turning it off disables reporting and binds that prefix to `NOTHING`; it does not restore a previous custom binding. The terminal must support the requested mouse modes.

A left-button press selects the visible window at the reported screen row. Wheel events scroll the current window backward or forward; the wheel handler does not select the window under the pointer first. Other reported button events are ignored.

## Example

```text
/set mouse on
/set mouse off
```

## Source references

- [source/keys.c](../../source/keys.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
