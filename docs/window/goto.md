# window goto

## Synopsis

```text
/window [<window>] goto <number>
```

## Description

Switch to the visible window at position `<number>` from the top of the screen. Positions start at 1: `goto 1` selects the top window, `goto 2` the second, and so on. The number is a screen position, not a window refnum or name.

With no window selector, use the current window's screen. An explicit selector chooses the screen containing that window. The destination becomes that screen's input window and EPIC's current window; subsequent operations in the same `/window` command apply to it. Its server becomes the command's server context.

This operation changes selection without rearranging, resizing, hiding, or swapping windows. Windows marked [window skip](skip.md) are still counted and can be selected. The previous input window is recorded for that screen's window history, and changing the current window invokes the `switch_windows` hook. No IRC request is sent, and successful execution normally produces no confirmation message.

## Arguments and edge cases

`<number>` is required. A missing or nonnumeric argument reports `GOTO: You must specify a number`. A negative number or a position larger than the number of visible windows reports `GOTO: Value is out of range`. Zero silently leaves selection unchanged.

Selecting an invisible window as the screen reference silently does nothing, because it has no screen. `/window -goto` is also a no-op. There is no wrapping from the bottom of the screen to the top.

After an unsuccessful ordinary `goto`, subsequent window operations use the current window, which may differ from an explicit screen-reference window. Additional words after the number are treated as subsequent window operations, not as `goto` options.

## Examples

```text
/window goto 1
/window goto 2
/window 7 goto 1
```

Select the top window, the second window, or the top window on the screen containing window 7, respectively. To select the second visible window and then change its name:

```text
/window goto 2 name chat
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_goto`, `get_number`, and `my_goto_window` implement position parsing and screen selection; `set_screens_current_window`, `make_window_current_by_refnum`, and `windowcmd` handle input selection, window history, hooks, and subsequent command context.
