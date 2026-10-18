# set always_split_biggest

## Synopsis

```text
/set always_split_biggest [on|off|toggle]
```

Default: `on`. With no value, [set](../commands/set.md) displays the current value; `toggle` reverses it. Other values are rejected as invalid boolean settings.

## Purpose

When [window new](../window/new.md) or [window show](../window/show.md) adds a window to a screen that already has windows, EPIC must choose an existing window to split. This setting controls that choice, not the number of windows or their exact final sizes.

## Description

- `on`: choose the largest eligible window on the destination screen, measured by its scrollable display-line count.
- `off`: prefer that screen's current input window if it has enough display lines for the added window's overhead; otherwise use the largest eligible window.

The largest-window search first excludes [fixed](../window/fixed.md) windows. If every window on the screen is fixed, it searches again including them. Fixed windows are therefore not absolutely exempt. With the setting off, the current-window preference also does not exclude a fixed window.

The current-window preference requires its display-line count to be strictly greater than the added window's visible toplines plus its status-line count. There is no fixed four-line threshold. Before applying that preference, EPIC also checks that the largest candidate can cover that overhead; otherwise it reports `Not enough room for another window!` and cannot place the added window through this split path.

The added window is placed above the selected window. Overhead and subsequent screen recalculation affect final geometry, so do not rely on an exact half-and-half division. The current sizing calculation begins with the largest candidate's display-line count even when the off setting selects another window to split.

Changing this setting has no immediate rearrangement effect; it is read when a later placement needs a split. It is global, applies to local screen layout, and sends no IRC request. A screen's first window does not need this selection, and creating a hidden window alone does not split a screen.

## Example

Prefer the current input window for the next split when it has sufficient space:

```text
/set always_split_biggest off
/window new
```

To restore selection of the largest eligible window:

```text
/set always_split_biggest on
```

## Source references

- [source/window.c](../../source/window.c): `add_to_window_list` finds candidates, applies the current-window preference, inserts the added window, and recalculates geometry.
- [source/vars.c](../../source/vars.c): boolean setting registration and standard value handling; no immediate layout callback is registered.
- [include/config.h](../../include/config.h): default value is on.
