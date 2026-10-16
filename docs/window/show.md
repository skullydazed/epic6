# window show

## Synopsis

```text
/window [<window>] show <destination>
```

## Description

Add a hidden (invisible) window to a screen and select it. If the destination is already visible, leave it on its existing screen and select it there. On success, it becomes EPIC's global current window and its screen's input window.

`<destination>` can be a refnum, [window name](name.md), or UUID. Lookup is global and case-insensitive for names and UUIDs. The argument is one whitespace-delimited word; use a refnum or UUID for a name containing spaces.

A hidden destination is added to the global current window's screen. If that current window is also hidden, EPIC uses the screen that most recently received input. A selector before `show` does not choose the insertion screen: `/window 3 show 7` still uses global current selection when placing hidden window 7.

Making room normally splits an existing visible window, inserting the destination above it. [set always_split_biggest](../set/always_split_biggest.md) controls donor selection; see [window new](new.md) for the splitting rules and current donor-height calculation limitation. This adds a split window rather than swapping another window out. Use [window swap](swap.md) or [window refnum_or_swap](refnum_or_swap.md) for replacement instead.

The shown window retains its contents, server, channels, queries, and settings. Its output height adapts to the available space. Attempting to add a hidden window resets its notified flag and activity indication, including when there is insufficient room and it remains hidden. No IRC request is sent.

## Restrictions and subsequent operations

The destination must have [window swappable](swappable.md) on. Otherwise EPIC reports `Window <name-or-number> can't be made visible`; this check also prevents selection through `show` when the destination is already visible. [window refnum](refnum.md) can select an unswappable visible window without changing visibility.

If there is insufficient room, EPIC can report `Not enough room for another window!` and return the destination to the hidden list. A failed show does not destroy it. **Once lookup succeeds, subsequent operations in the same `/window` command target the destination even if showing it fails.** Global current selection can therefore differ from the subsequent operation target.

A missing destination reports `SHOW: Please specify a window refnum or name`; an unknown one reports `SHOW: No such window: <destination>`. These lookup failures leave the original command target in place and allow subsequent operations to continue.

There are no other options. Additional words are processed as subsequent window operations. `/window -show` is a no-op. Successful showing normally produces no confirmation message; changing current selection invokes normal selection hooks.

## Examples

```text
/window show 3
/window show chat
/window show 3 name chat
```

Show and select window 3 or the window named `chat`. The last example names window 3 after attempting to show it, even if showing fails.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_show`, `get_window_from_args`, `get_window_by_desc`, and `show_window` implement lookup, placement, selection, and failure targeting; `add_to_window_list` implements splitting and notification reset; `set_screens_current_window` updates input history.
