# window kill

## Synopsis

```text
/window [<window>] kill
```

## Description

Destroy the selected window. With no window selector, destroy the current window. Its scrollback, lastlog history, query/nick list, pending channel associations, and private settings are deleted. This is irreversible; use [window hide](hide.md) to retain the window or [window merge](merge.md) when you want to preserve its contents elsewhere.

Channels are not explicitly [parted](../commands/part.md). EPIC attempts to move each channel to another window on the same server, preferring windows that do not have both `fixed` and `skip` on. If none exists, there is no replacement window for those channels.

If this removes the last window associated with a server, normal server checking closes that connection when the server's `autoclose` property is enabled, as it is by default. This is a connection shutdown rather than an IRC `kill` request. With autoclose disabled, removing the last window does not by itself require closing the connection. Saved log files are not erased.

## Restrictions

A window with [window killable](killable.md) off cannot be killed; EPIC reports `You cannot kill an unkillable window`. New windows default to killable. [window swappable](swappable.md) off does not by itself prevent killing.

For the visible-window safety check, EPIC treats a window as fixed only when **both** [window fixed](fixed.md) and [window skip](skip.md) are on:

- Such a window cannot be killed if it is the only visible window on its screen, even if hidden windows exist.
- Any other visible window cannot be killed if it is the last visible window outside that category and no hidden window exists to replace it.

A refusal reports `You can't kill the only window!` and leaves the window intact. Invisible windows are not subject to this screen-size restriction.

## Process and selection

After the restrictions pass, EPIC invokes the `window_beforekill` hook with the window's refnum. This is a notification; its return value does not veto deletion. EPIC marks the window deceased and reassigns its channels before removing it.

An invisible window is removed from the hidden list. A visible window is normally removed and the remaining windows expand to fill the screen. When it is the last visible window outside the fixed-and-skipped category, EPIC swaps a hidden window into its place, preferring a swappable hidden window and falling back to another hidden window if necessary.

Input and current-window selection are repaired as needed so they do not reference the destroyed window. Subsequent operations in the same `/window` command target EPIC's resulting current window. After private data is deleted, the `window_kill` hook receives the old refnum and the old name, or the refnum again if it had no name.

## Arguments and output

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations. An unkillable-window refusal stops those operations; a refusal from the visible-window safety check returns the current window and permits them to continue. `/window -kill` is a no-op. Successful deletion normally produces no confirmation message, although channel reassignment or hooks can produce output.

## Examples

```text
/window kill
/window 3 kill
```

Destroy the current window or window 3, subject to the restrictions above.

## History

This operation already existed in ircII2.1.5g.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_kill`, `unlink_window`, `count_fixed_windows`, `swap_window`, and `delete_window_contents` implement checks, hooks, replacement, and deletion; `window_check_servers` enforces server autoclose.
- [source/names.c](../../source/names.c): `reassign_window_channels` searches for replacement channel windows without sending `part`.
- [source/server.c](../../source/server.c): initializes the server `autoclose` property to on.
