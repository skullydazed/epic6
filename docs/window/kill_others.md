# window kill_others

## Synopsis

```text
/window [<window>] kill_others
```

## Description

This operation is intended to perform [window kill](kill.md) on other windows while retaining the selected window. **The current implementation does not select the other visible windows on the screen.** Its actual behavior depends on the selected window's visibility:

- If the selected window is visible, it destroys killable **hidden windows across all servers**. Other visible windows remain untouched.
- If the selected window is hidden, it silently does nothing.

With no window selector, the selected window is the current window. Do not rely on this operation to leave only one visible window or one hidden window. To retain one visible window while keeping other windows available, use [window hide_others](hide_others.md). To explicitly delete hidden windows, use [window kill_all_hidden](kill_all_hidden.md).

## Deletion effects

Windows with [window killable](killable.md) off are silently skipped. For a hidden window, `fixed`, `skip`, and `swappable` do not prevent deletion. Each deleted window loses its scrollback, lastlog history, query/nick list, pending channel associations, and private settings. Deletion is irreversible; saved log files are not erased.

As with `window kill`, channels are not explicitly parted. EPIC attempts to reassign them to another window on the same server. Each deletion invokes `window_beforekill` before removal and `window_kill` after cleanup; the before-kill hook's return value does not veto deletion.

If deletion leaves a server without an associated window, normal server checking closes its connection when its `autoclose` property is enabled, as it is by default. This is not an IRC `kill` request.

## Arguments and output

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations, retaining the original selected window as their target. `/window -kill_others` is a no-op. Successful execution normally produces no confirmation message, although hooks or channel reassignment can produce output.

## Example

```text
/window 3 kill_others
```

If window 3 is visible, delete eligible hidden windows globally while retaining all visible windows. If window 3 is hidden, do nothing.

## History

This operation already existed in ircII2.1.5g.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_kill_others` chooses the invisible list for a visible target and a screen list for an invisible target; `unlink_window` and `delete_window_contents` perform deletion; `window_check_servers` handles autoclose.
- [source/screen.c](../../source/screen.c): `get_screen_by_refnum` and `get_screen_window_list` return no screen/list for the hidden-window screen value of -1, so the invisible-target branch does not iterate.
- [source/names.c](../../source/names.c): `reassign_window_channels` searches for replacement channel windows.
