# set lastlog

## Synopsis

```text
/set lastlog [<number>]
```

Compiled default: `256`. With no number, [set](../commands/set.md) displays the current value. Supply a non-negative integer; negative or nonnumeric values are rejected. Existing window heights can raise the effective value as described below.

## Description

Sets the lastlog retention limit for every existing window and the initial limit copied by future windows. Lastlog stores output records that can be searched with [lastlog](../commands/lastlog.md) and used when rebuilding display scrollback. The limit counts records selected by the window's lastlog-level mask, not wrapped terminal rows or bytes.

Changing the setting applies the requested limit to each existing window, trims its oldest records as needed, and requests a scrollback rebuild. Increasing the limit does not recover records already removed. Reducing it can permanently remove retained output.

## Minimum and zero behavior

The current callback trims **before** enforcing a minimum of twice each window's scrolling output height. It then raises each window's limit to at least that minimum and raises the stored global setting to at least the largest such minimum among existing windows.

Consequently, setting `lastlog` to zero does not reliably disable retention. It can immediately remove counted history, then leave positive limits in windows with a nonzero display height. The stored value can likewise become positive. Use zero with that destructive trimming behavior in mind, not as a persistent off switch.

Windows of different heights can end up with different limits after one assignment, even though the displayed global setting is raised to cover the largest minimum. New windows copy that resulting global value. Individual [window lastlog](../window/lastlog.md) operations have their own behavior.

## Level and memory scope

[Lastlog_level](lastlog_level.md) selects which output levels count as visible lastlog records. Records outside that mask can still exist internally for display reconstruction. The trimming path removes oldest records until the counted size fits; it can remove uncounted records encountered along the way, but does not guarantee that every uncounted record is removed.

This setting is therefore not a strict bound on all stored output or memory consumption. It is also separate from file logging and the number of wrapped display rows maintained by [scrollback](scrollback.md). Changing it sends no IRC request.

## Example

Allow up to 2000 counted records per window, subject to the height-based minimum:

```text
/set lastlog 2000
```

This updates existing windows as well as the default for later ones. Query the resulting global value with `/set lastlog`.

## Source references

- [source/lastlog.c](../../source/lastlog.c): `set_lastlog_size` applies, trims, and then enforces minimums; `add_to_lastlog` marks counted records; `trim_lastlog` removes oldest records until counted size fits.
- [source/window.c](../../source/window.c): `new_window` copies the global limit; per-window accessors maintain limits and sizes.
- [source/vars.c](../../source/vars.c): integer registration and validation.
- [include/config.h](../../include/config.h): compiled default 256.
