# window new

## Synopsis

```text
/window [<window>] new
```

## Description

Create a new split (visible) window on the selected window's screen. With no window selector, use the current window's screen. EPIC inserts the new window above the window chosen to supply its space and makes the new window the input and global current window. Subsequent operations in the same `/window` command target the new window.

[set always_split_biggest](../set/always_split_biggest.md), compiled default on, controls which window is split. EPIC first finds the largest non-fixed output area, falling back to fixed windows if all are fixed; ties favor the first in screen order. With the setting off, it instead uses that screen's input window if its output area is larger than the new window's status/topline overhead. This choice can split a fixed input window too.

Normally, EPIC subtracts the new window's overhead from the donor's output space and divides the remainder in half, giving the new window an extra row when needed. In the current implementation, when the input-window override chooses a different donor, the calculation still starts from the largest candidate's height; screen recalculation then corrects the total. Do not expect an exact half split in that case.

## Initial settings

The new window uses the global current window's server, even when an explicit selector chooses a screen elsewhere. It starts unnamed, without channels, queries, or stored output. Its user-visible reference number is the lowest unused positive refnum. Use [window number](number.md) to change it; having channels does not prohibit renumbering.

The built-in initialization includes:

- [window level](level.md) none for additional windows.
- Defaults copied from [set notify_level](../set/notify_level.md), [set scrollback](../set/scrollback.md), [set hold_slider](../set/hold_slider.md), [set lastlog_level](../set/lastlog_level.md), [set lastlog](../set/lastlog.md), and [set indent](../set/indent.md).
- Hold mode off, hold interval 10, one status row, and no toplines.
- `killable`, `swappable`, and `scrolladj` on; `fixed`, `skip`, invisible notification, and window logging off.

EPIC invokes `window_create` with the new user-visible refnum after setting up its display. Scripts can change these defaults. In particular, [script/hold_mode](../../script/hold_mode) supplies `/set hold_mode` and applies it through that hook; it is not a built-in constructor setting.

## Limits and arguments

If the new window cannot fit, EPIC can report `Not enough room for another window!` and retain it as an **invisible** window rather than cancel creation. It still becomes the global current window. Selecting an invisible window as the screen reference likewise creates another invisible window, without changing a screen's input window. Use [window show](show.md) to display it later or [window new_hide](new_hide.md) to create a hidden window intentionally.

Outside fullscreen mode, additional window creation is refused silently and subsequent window operations stop. Exhausting available reference numbers reports `window new: All refnums in use, sorry` and also stops processing.

There are no arguments or options specific to `new`. Following words are processed as subsequent window operations. `/window -new` also creates a window; the handler does not treat the minus form as a no-op. Creation alone sends no IRC request or channel join, though hooks can run commands.

## Examples

```text
/window new
/window new name chat
```

Create a window, or create it and name it `chat`. Check visibility with [window list](list.md) if the screen had little room.

## History

This operation already existed in ircII2.1.5g.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_new`, `new_window`, and `add_to_window_list` implement creation, defaults, refnums, splitting, hidden fallback, and the create hook; `recalculate_windows` fits the screen; `windowcmd_number` permits renumbering.
- [include/config.h](../../include/config.h): defines the default `always_split_biggest` value.
- [script/hold_mode](../../script/hold_mode): supplies script-based hold defaults through `window_create`.
