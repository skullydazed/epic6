# window kill_all_hidden

## Synopsis

```text
/window [<window>] kill_all_hidden
/window [<window>] kill_all_invisible
```

## Description

Destroy all hidden (invisible) windows that are [window killable](killable.md). Unkillable windows are silently skipped. Visible windows are not selected for deletion. `kill_all_invisible` is an equivalent built-in spelling.

The hidden-window list is global: this operation is not limited to the selected window's server or screen. A window selector does not protect that window; if it is hidden and killable, it is deleted too. If there are no eligible hidden windows, nothing is deleted.

Each deletion uses the same cleanup as [window kill](kill.md): scrollback, lastlog history, query/nick lists, pending channel associations, and private settings are deleted. This is irreversible. Channels are not explicitly parted; EPIC attempts to reassign them to another window on the same server. A replacement can itself be another hidden window that is deleted later in the operation.

Each deleted window receives a `window_beforekill` notification before removal and a `window_kill` notification after cleanup. The before-kill hook's return value does not veto deletion. Saved log files are not erased.

If deleting these windows leaves a server with no associated window, normal server checking closes its connection when its `autoclose` property is enabled, as it is by default. This operation does not send an IRC `kill` request.

## Current window and arguments

If the global current window is deleted, EPIC normally makes the input window on the screen that most recently received input current. It has fallbacks to a visible window if needed. If the window selected by the `/window` command is deleted, subsequent operations in that command target the resulting current window. Otherwise, subsequent operations keep their original selected target.

The visible-window safety restriction of `window kill` does not prevent deletion of hidden windows. Neither `fixed`, `skip`, nor `swappable` protects a hidden window from this operation; use `killable off` for that purpose.

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations. `/window -kill_all_hidden` and `/window -kill_all_invisible` are no-ops. Successful deletion normally produces no confirmation message, although hooks or channel reassignment can produce output.

## Examples

```text
/window kill_all_hidden
/window kill_all_invisible
```

Both destroy every eligible hidden window across all servers. To protect hidden window 3 before deleting the others:

```text
/window 3 killable off kill_all_hidden
```

## History

This operation first appeared in EPIC5-0.0.3.

## Source references

- [source/window.c](../../source/window.c): the command table maps both names to `windowcmd_kill_all_invisible`; `unlink_window`, `delete_window_contents`, and `make_window_current_by_refnum` implement deletion and current-window repair; `window_check_servers` handles autoclose.
- [source/names.c](../../source/names.c): `reassign_window_channels` searches for replacement windows on the same server.
- [source/server.c](../../source/server.c): initializes server autoclose to on.
