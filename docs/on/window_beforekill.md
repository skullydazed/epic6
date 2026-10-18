# on window_beforekill

## Synopsis

```text
/on [<noise>]window_beforekill "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered just before EPIC unlinks and destroys a window. This is your last opportunity to query and save window data before its scrollback, lastlog, and other private state are removed.

It is permissible to query the window, but do not change it during this hook, particularly its visibility, identity, or existence. The destruction routine is already using its current state; changing that state or recursively killing the same window can invalidate its assumptions and crash the client. Use [defer](../commands/defer.md) for follow-up changes to surviving windows, after saving any data needed from the dying window.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the window refnum. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | User-visible refnum of the window about to be destroyed. |

The window is still available for queries at this stage and has not yet been marked deceased. It need not be the current input window; use the supplied refnum explicitly.

## Ordering and effects

The hook runs after initial validity and only-window checks. A rejected attempt to kill the only eligible window does not reach it. Its suppression result is ignored: a `^window_beforekill` handler cannot veto destruction, and this event has no separate built-in output.

Afterward, EPIC marks the window deceased, reassigns its channels as appropriate, removes it from its screen or hidden-window list, and frees its private data. During normal operation, [on window_kill](window_kill.md) runs later, after the window has been freed; it cannot query the former window's data.

Normal client shutdown also uses the pre-kill hook while deleting windows. The later `window_kill` event is skipped during that shutdown. Do not depend on deferred work running during client exit, or interpret a deferred refnum as a guarantee that the old window still exists.

This hook is about a local window, not leaving IRC. It does not itself send a part or quit request.

## Example

Record which window is about to disappear without changing it:

```text
on -window_beforekill "*" (win) {
    echo Saving cleanup information for window $win;
};
```

A real cleanup handler should query and copy the information it needs at this point, before scheduling any later work.

## Source references

- [source/window.c](../../source/window.c): `unlink_window` performs preliminary checks and invokes the hook before marking the window deceased; `delete_window_contents` frees history and data, then invokes `window_kill` outside shutdown; `delete_all_windows` uses the same pre-kill path.
- [source/irc.c](../../source/irc.c): normal exit deletes all windows.
- [source/commands.c](../../source/commands.c): `defercmd` queues commands for later execution.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
