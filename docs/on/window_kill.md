# on window_kill

## Synopsis

```text
/on [<noise>]window_kill "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered after a window has been killed during normal client operation. By this point the window has been irrevocably destroyed and cannot be used. This hook is useful for cleaning up your own script data keyed by the former window's refnum.

If you need to query or save window information before destruction, use [on window_beforekill](window_beforekill.md).

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the former refnum and name together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | User-visible refnum of the killed window. |
| `$1` | Former window name, or its refnum as text if it had no name. |

These are saved values, not references to a live window. The refnum may later be reused for a different window. Do not use it to query the former window or schedule operations assuming it still exists.

## Timing and effects

EPIC first invokes `window_beforekill`, then unlinks the window from its visible or hidden list and frees its private data, including scrollback and lastlog. The `window_kill` hook runs after both its internal and user-visible lookup entries have been cleared and its storage freed.

The hook's suppression result is ignored: it cannot reverse deletion, and the event has no separate built-in display. An unsuccessful kill attempt does not reach this event. Merely hiding, swapping, or renaming a window does not destroy it and does not generate this event.

Normal client shutdown deletes windows through the same cleanup machinery but explicitly skips `window_kill`. Use [on exit](exit.md) or `window_beforekill` for shutdown-related work rather than depending on this hook.

The event concerns local window destruction; it does not itself send a part or quit request.

## Example

Observe completed destruction without querying the former window:

```text
on -window_kill "*" (win, name) {
    echo Window $win ($name) has been destroyed.;
};
```

A cleanup handler can also remove its own bookkeeping for `$win` at this point.

## History

This hook already existed in ircII2.1.5g. Its current two-parameter form appeared in EPIC4-1.1.11.

## Source references

- [source/window.c](../../source/window.c): `unlink_window` runs pre-kill handling; `delete_window_contents` saves identifying values, frees the window, and invokes this hook only outside shutdown; `delete_all_windows` handles shutdown cleanup.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
- [source/irc.c](../../source/irc.c): normal exit invokes window cleanup.
