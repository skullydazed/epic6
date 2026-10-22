# set automargin_override

## Synopsis

```text
/set automargin_override [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value.

## Description

Controls whether EPIC assumes it can use the terminal’s last column. When off, EPIC uses that column only when its terminal data includes both automatic margins (`am`) and the capability to disable them (`rmam`); otherwise it reserves a column. When on, it attempts to disable automatic margins and use the last column regardless of those checks.

Changing the setting in fullscreen mode recalculates this terminal behavior and requests a redraw. Use the override only when the terminal can handle the last column correctly.

## Example

```text
/set automargin_override on
```

## Source references

- [source/term.c](../../source/term.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
