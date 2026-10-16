# window killable

## Synopsis

```text
/window [<window>] killable [on|off|toggle]
```

## Description

Specify whether the selected window can be destroyed with [window kill](kill.md). With no window selector, use the current window. New windows default to `on`. `off` protects the window, `on` permits killing it subject to the normal window restrictions, and `toggle` reverses the setting. Without an argument, report the current value.

When this setting is off, `window kill` reports `You cannot kill an unkillable window` and leaves the window intact. [window killswap](killswap.md) also refuses an unkillable window. [window kill_all_hidden](kill_all_hidden.md), its `kill_all_invisible` synonym, and [window kill_others](kill_others.md) silently skip unkillable candidates.

The flag is not a general protection against every operation. It does not prevent hiding or swapping the window, clearing its contents, or disconnecting its server. [window delete_kill](delete_kill.md) bypasses this flag, and client shutdown deletes all windows regardless of it. [window merge](merge.md) moves data before calling the ordinary kill operation, so `killable off` can prevent the final deletion without preventing the earlier data transfers.

## Output and arguments

Queries and valid changes report `Window KILLABLE is ON` or `Window KILLABLE is OFF`. Invalid values report `Value for KILLABLE must be ON, OFF, or TOGGLE` and leave the setting unchanged. A query or invalid value stops subsequent operations in the same `/window` command. Additional words after a valid value are processed as subsequent window operations. `/window -killable` is a no-op.

Changing this flag does not itself kill or move the window. It works on visible and invisible windows and sends no IRC request.

## Examples

```text
/window killable off
/window 3 killable
/window 3 killable toggle
/window 3 killable on kill
```

Protect the current window, report or reverse window 3's protection, or allow window 3 to be killed and then attempt to kill it.

## Source references

- [source/window.c](../../source/window.c): `new_window`, `windowcmd_killable`, and `get_boolean` define the default, arguments, and reports; `windowcmd_kill`, `windowcmd_killswap`, `windowcmd_kill_all_invisible`, and `windowcmd_kill_others` check protection. `windowcmd_delete_kill` and `delete_all_windows` bypass it; `windowcmd_merge` transfers data before calling `windowcmd_kill`.
- [source/irc.c](../../source/irc.c): client shutdown calls `delete_all_windows`.
