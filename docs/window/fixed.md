# window fixed

## Synopsis

```text
/window [<window>] fixed [on|off|toggle]
```

## Description

Set whether the selected window has a fixed output height. With no window selector, use the current window. New windows default to `off`. `on` enables the flag, `off` disables it, and `toggle` reverses it. With no argument, report the current value without changing it.

Fixed windows are excluded from [window balance](balance.md). During terminal resizing, EPIC normally gives or takes output rows from non-fixed windows first. You can still explicitly resize a fixed window with [window size](size.md), [window grow](grow.md), or [window shrink](shrink.md).

This is useful for an operator information window that should keep its output height while the other windows are balanced. It does not reserve a screen position: moving or resizing neighboring windows can move the fixed window vertically, and moving another window can change their order.

## Limitations

The flag is a preference rather than an absolute size lock. If all windows are fixed, terminal resizing can resize them, and creating another visible window can split a fixed window. If a terminal becomes too small, EPIC can hide windows, including fixed windows when necessary.

In the current implementation, resizing another window can also resize a fixed window: the resize helper's fixed-window check does not always exclude it from selection as the window that supplies or receives rows. Do not rely on `fixed on` to prevent every indirect resize.

The operation reports `Window FIXED is ON` or `Window FIXED is OFF`. Invalid values report `Value for FIXED must be ON, OFF, or TOGGLE` and leave the flag unchanged. A query or invalid value stops processing subsequent operations in the same `/window` command. `/window -fixed` is a no-op. Changing the flag alone does not rebalance windows or send an IRC request.

## Examples

```text
/window 3 fixed on
/window balance
/window 3 fixed
/window 3 fixed toggle
```

The first command marks window 3 fixed; the second balances the other non-fixed windows on the current screen. The final commands report and then reverse window 3's setting.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_fixed`, and `get_boolean` define the default, arguments, and reports; `rebalance_windows`, `recalculate_windows`, `add_to_window_list`, `resize_window`, and `move_window_to` implement the size and position interactions.
