# window skip

## Synopsis

```text
/window [<window>] skip [on|off|toggle]
```

## Description

Choose whether navigation skips the selected window. With no window selector, use the current window. New windows default to off. `on` enables skipping, `off` disables it, and `toggle` reverses it. Without an argument, report the current value.

The [next_window](../bind/next_window.md) and [previous_window](../bind/previous_window.md) bindings skip marked visible windows while cycling around a screen. If a complete cycle finds no other selectable window, they can remain on the starting window, even if it is marked skipped.

[window next](next.md) and [window previous](previous.md), and their swap bindings, also exclude skipped hidden windows from their numbered candidate searches. This setting does not remove a window from the screen or suppress its output.

Skipped windows can still be selected explicitly with [window goto](goto.md) or [window refnum](refnum.md). These are not the only routes: [window last](last.md), explicit [window show](show.md), and explicit [window swap](swap.md) do not filter candidates by `skip`. Other operations and scripts can select the window too. `skip on` is not a general ban on making it current.

## Fixed-window interactions

The combination of `skip on` and [window fixed](fixed.md) on also affects window management. Such windows are excluded from the ordinary-window count used by [window hide](hide.md) and [window kill](kill.md) safety checks. Consequently, hiding or killing another window can be refused even when several windows are visible. Channel reassignment during detachment or deletion also prefers windows without both flags. See those operations for their exact restrictions.

Changing the flag alone does not move, resize, hide, or change input selection, and it does not alter the window's server, channels, or routing levels. It works on visible and invisible windows and sends no IRC request.

## Output and arguments

Queries and valid changes report `Window SKIP is ON` or `Window SKIP is OFF`. Invalid values report `Value for SKIP must be ON, OFF, or TOGGLE` and leave the setting unchanged. A query or invalid value stops subsequent operations in the same `/window` command.

Additional words after a valid value are processed as subsequent window operations. `/window -skip` is a no-op.

## Examples

```text
/window 3 skip on
/window 3 skip
/window 3 skip toggle
/window refnum 3
```

Exclude window 3 from cycling, report or reverse its flag, or explicitly select it even when skipped.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_skip`, and `get_boolean` implement defaults and reports; `get_next_window`, `get_previous_window`, `windowcmd_next`, and `windowcmd_previous` apply navigation filters; `count_fixed_windows`, `hide_window`, and `unlink_window` use the fixed-and-skipped category.
- [source/names.c](../../source/names.c): `reassign_window_channels` deprioritizes windows with both flags.
