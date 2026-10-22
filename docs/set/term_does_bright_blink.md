# set term_does_bright_blink

## Synopsis

```text
/set term_does_bright_blink [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value.

## Description

This setting is registered and can be queried or changed, but the current implementation does not read its value or attach a callback. Changing it has no builtin terminal or display effect. Scripts can still inspect its stored value.

## Example

```text
/set term_does_bright_blink
```

## Source references

- [source/vars.c](../../source/vars.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
