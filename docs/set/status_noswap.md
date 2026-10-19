# set status_noswap

## Synopsis

```text
/set status_noswap [<text>]
/set -status_noswap
```

Default: `(NOSWAP)`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the marker rather than restoring the default.

## Description

Sets the string displayed when a window is not swappable, as configured by [window swappable](../window/swappable.md) off. It is represented by `%{1}W` in [status_format](status_format.md), or a per-window format. When the window is swappable, the field is empty.

The marker uses the state of the window whose status line is being drawn, not only the selected input window. [Show_status_all](show_status_all.md) does not restrict this field. Plain `%W` is a different field for the current-window marker; it does not display `status_noswap`.

The text is inserted directly. `%{1}W` inside this setting is literal, not a nested substitution. The outer format must include `%{1}W` for the marker to appear.

## Effects and expansion

Changing the string rebuilds status formats and requests updates. It does not make a window swappable or nonswappable, prevent a swap itself, or send an IRC request. Hiding the marker does not change the window's swap policy.

Ordinary command parsing can expand dollar expressions while assigning the value. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can additionally expand afterward.

## Example

```text
/set status_noswap [noswap]
```

A nonswappable window with `%{1}W` in its status format displays `[noswap]`. Use `/set -status_noswap` to hide that text without changing its swappable flag.

## History

`set status_noswap` first appeared in EPIC4-1.2.2.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default marker.
- [source/status.c](../../source/status.c): `%{1}W` maps to `status_swappable`, which returns the text only when the flag is off.
- [source/window.c](../../source/window.c): `get_window_swappable` and `windowcmd_swappable` manage the separate window policy.
