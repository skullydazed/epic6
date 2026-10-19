# set status_window

## Synopsis

```text
/set status_window [<text>]
/set -status_window
```

Default: `^^^^^^^^`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the marker rather than restoring the default.

## Description

Sets the status-bar text normally used to identify the current input window in a split screen. The plain `%W` field in [status_format](status_format.md) displays it only when the window is the actual input window of its screen and that screen has more than one visible window. Otherwise the field is empty.

The outer field can select other conditions:

| Outer field | When this text appears |
| --- | --- |
| `%W` | Current input window on a screen with multiple visible windows. |
| `%{2}W` | Every window whose status format includes the field. |
| `%{3}W` | Current input window, even when it is the screen's only visible window. |

These current-window tests are per screen and are not overridden by [show_status_all](show_status_all.md). `%{1}W` is a different field using [status_noswap](status_noswap.md); it does not use this text.

The marker is inserted directly. `%W` inside `status_window` is literal, not a nested substitution. An applicable field must be present in the global or per-window status format for the marker to appear.

## Effects and expansion

Changing this string rebuilds status formats and requests updates. It does not select a window, split the screen, or change swap policy. Ordinary command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can additionally expand afterward.

## Example

Customize the selected-window marker:

```text
/set status_window ::ACTIVE::
```

A plain `%W` shows this marker only for the input window in a split screen. To show it on the selected window even without a split, use `%{3}W` in the outer status format. Hide all three variants with `/set -status_window` without changing the selected window.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default marker.
- [source/status.c](../../source/status.c): `status_window` implements the distinct map branches; `IS_CURRENT_WINDOW` checks the input window of the window's screen.
