# screen script

## Synopsis

```text
/load screen
```

## Description

This script lets you manipulate EPIC windows using a set of Control-X key sequences reminiscent of the screen program. It defines the `win.newwin` alias and installs bindings; loading alone does not create additional windows or start an external terminal multiplexer.

In the table, `^X` means Control-X. Press the following key afterward. Ordinary uppercase and lowercase letters are distinct; `^C` means Control-C rather than uppercase `C`.

| Sequence | Action |
| --- | --- |
| `^X1` through `^X9` | Swap the current window with hidden window refnum 1 through 9. |
| `^X0` | Swap with hidden window refnum 10. |
| `^Xa` or `^X^X` | Swap with the first window on the invisible-window list, through `window swap last`. |
| `^Xc` or `^X^C` | Run `win.newwin`, creating a window, hiding it, then swapping with `last`. |
| `^Xk` | Run `window killswap`: bring in a hidden window and destroy the former current window. |
| `^Xl` | Run `window list`. |
| `^Xn` | Swap with the next hidden window in numeric refnum order. |
| `^Xp` | Swap with the previous hidden window in numeric refnum order. |
| `^XC` | Run `clear all`, clearing visible and hidden windows, not only those on screen. |

The source also binds the spelling `^X^` followed by a backtick to `swap_next_window`. The current key parser treats caret-backtick as literal characters, so this means Control-X, literal `^`, then backtick. It does **not** install Control-X Space. Use `^Xn` for the straightforward next-hidden-window action.

## Effects and limitations

These are swaps with hidden windows, not general focus selection among visible windows. A numbered target that is missing or already visible produces the normal window diagnostic. `last` refers to the invisible-list head, not the highest refnum. Next and previous use the hidden-window numbering order and wrap according to the built-in window commands.

The create alias runs `window new hide` followed by `window swap last`. Normal window-creation, hiding, and swapping constraints still apply. `killswap` refuses an unkillable current window or the absence of a hidden replacement; destruction loses that window's private display state. Its usual window cleanup and channel reassignment behavior applies. Clearing through `^XC` changes display views through the built-in clear command; it is not a command to delete every window.

Loading replaces the listed bindings in the current keymap without saving their previous assignments. There is no script setting, automatic restoration, or package declaration for unloading these definitions. Another script can replace the same sequences later. The terminal must deliver Control-X and any subsequent control character to EPIC; terminal or multiplexer interception can prevent a binding from running.

No external screen program, helper script, optional configure feature, or IRC server extension is required. The bindings mostly operate on local windows; they do not themselves send a separate IRC request. Normal effects of creating or destroying windows still follow the underlying window commands. There is no function-return interface.

## Example

Load the script, then press Control-X followed by lowercase `c` to create and display a new window. Press Control-X followed by lowercase `a` to swap back to the hidden window, or lowercase `l` to list windows. The create alias can also be invoked directly:

```text
/win.newwin
```

## Other notes

The script was written by Matthew Green (phone), inspired by Darren Reed (Avalon).

## Source references

- [script/screen](../../script/screen): alias and exact installed bindings.
- [source/keys.c](../../source/keys.c): key-sequence parsing and binding replacement.
- [source/window.c](../../source/window.c): `windowcmd_new`, `windowcmd_hide`, `windowcmd_swap`, `windowcmd_killswap`, hidden-window selection, and clear-all behavior.
- [source/commands.c](../../source/commands.c): `e_clear` interprets `clear all` without restricting it to visible windows.
