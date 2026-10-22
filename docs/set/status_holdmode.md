# set status_holdmode

## Synopsis

```text
/set status_holdmode [<text>]
/set -status_holdmode
```

Default: ` (Hold)` (with a leading space). With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Sets the literal marker used by `%{1}H` in [status_format](status_format.md). It appears only when hold mode is enabled for that window and both the holding distance and scrollback distance are strictly less than the window’s display height. At the exact height boundary this marker is empty.

This is separate from [status_hold](status_hold.md), which marks output beyond the visible held or scrollback view. Changing the string rebuilds status formats; it does not enable hold mode. Unsetting hides the marker.

## Example

```text
/set status_holdmode [Hold]
```

## Source references

- [source/status.c](../../source/status.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
