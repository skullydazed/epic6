# on window_server

## Synopsis

```text
/on [<noise>]window_server "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC updates a window's server association through its normal association-change routine. This includes [window server](../window/server.md), [window discon](../window/discon.md), and bulk reassociation performed by [server](../commands/server.md).

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the window refnum and both server refnums together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | User-visible window refnum. |
| `$1` | Old server refnum. |
| `$2` | New server refnum. |

The special server refnum `-2` means no server association. It can occur as the old value when associating a previously unassociated window, or as the new value when disassociating it. This describes the window's association, not necessarily a change in a network connection's state.

## Timing and effects

The window's server field has already been assigned its new value when the hook runs. Its suppression result is ignored: it cannot cancel reassociation, and this event has no separate built-in output. Status updates are requested after the hook returns.

Newly created windows inherit their initial association directly and do not generate this event for that assignment. The low-level setter used during startup also assigns the field directly without this hook. Thus it is not a notification for every internal assignment.

The change routine does not itself check whether the old and new values differ. Some callers avoid redundant assignments, but repeated disassociation can still report `-2` for both values. A bulk server change invokes the event separately for each matching window; do not assume all windows or the primary-server selection have already been updated during the first handler.

A server can disconnect while a window retains its server association, and association can change before a new connection becomes active. Use [on server_status](server_status.md) or [on connect](connect.md) for those separate events. The hook itself sends no IRC request; the surrounding server command can initiate other connection work afterward.

## Example

Observe association changes, including the `-2` sentinel:

```text
on -window_server "*" (win, oldserver, newserver) {
    echo Window $win server association: $oldserver -> $newserver;
};
```

## History

This hook first appeared in EPIC5-0.3.2.

## Source references

- [source/window.c](../../source/window.c): `window_change_server` assigns the new value before the hook; `change_window_server` performs bulk reassociation; the constructor and `set_window_server` assign directly.
- [source/server.c](../../source/server.c): server switching calls bulk window reassociation.
- [source/irc.c](../../source/irc.c): startup uses the direct setter for the default server.
- [include/server.h](../../include/server.h): defines the no-server sentinel as `-2`.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
