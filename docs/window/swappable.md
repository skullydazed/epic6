# window swappable

## Synopsis

```text
/window [<window>] swappable [on|off|toggle]
```

## Description

Control whether ordinary window operations may change the selected window's visibility. With no window selector, use the current window. New windows default to on. `off` prevents ordinary hiding or showing, `on` permits those operations subject to their other checks, and `toggle` reverses the flag. Without an argument, report it.

[window hide](hide.md) refuses to hide an unswappable window. [window show](show.md) refuses it even when it is already visible and would only need selection. Explicit [window swap](swap.md) requires both participants to be swappable. [window next](next.md) and [window previous](previous.md) exclude unswappable hidden candidates; [window hide_others](hide_others.md) silently skips unswappable windows.

This is not a restriction on every kind of selection. [window refnum](refnum.md), [window goto](goto.md), and normal visible-window cycling can still select an unswappable visible window. It also does not prevent resizing or deletion; use [window fixed](fixed.md) or [window killable](killable.md) for their respective controls and limitations.

## Internal fallback and exceptions

The internal swap helper can choose a replacement without an explicit destination, such as when deleting the last ordinary visible window. It prefers a swappable hidden window; if none exists, it can use the first hidden window even with swappability off. The deletion path also enables swappability on the window being removed when it needs that replacement.

This fallback does **not** apply to ordinary `/window swap last`, which supplies an explicit destination and still checks its flag. [window last](last.md) is visible-window history selection, not this hidden-window fallback.

Other maintenance paths can change visibility without consulting this flag, including hiding windows when a screen becomes too small and removing a secondary screen. `swappable off` is therefore not an absolute visibility lock. [window killswap](killswap.md) also attempts deletion after a failed initial swap; swappability alone is not deletion protection.

## Output and arguments

Queries and valid changes report `Window SWAPPABLE is ON` or `Window SWAPPABLE is OFF`. Invalid values report `Value for SWAPPABLE must be ON, OFF, or TOGGLE` and leave the setting unchanged. A query or invalid value stops subsequent operations in the same `/window` command.

A successful change requests a status update. Additional words after a valid value are processed as subsequent window operations. `/window -swappable` is a no-op. Changing the flag alone does not show, hide, resize, or select the window, and sends no IRC request.

## Examples

```text
/window 3 swappable off
/window 3 swappable
/window 3 swappable toggle
/window 3 swappable on
```

Prevent ordinary visibility changes for window 3, report or reverse its flag, or permit visibility changes again. The flag can be changed while the window is hidden.

## History

This operation first appeared in EPIC4-1.2.2.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_swappable`, and `get_boolean` implement defaults and reports; `hide_window`, `show_window`, and `swap_window` enforce ordinary visibility checks. `unlink_window` uses the internal replacement fallback; `recalculate_windows` can hide windows for space; `windowcmd_killswap` attempts post-swap deletion.
- [source/screen.c](../../source/screen.c): `kill_screen` detaches windows when destroying a secondary screen.
