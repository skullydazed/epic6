# on window_create

## Synopsis

```text
/on [<noise>]window_create "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered whenever EPIC successfully creates a new window, allowing scripts to give each window additional defaults. It applies to windows created visible or hidden, including [window new](../window/new.md) and [window new_hide](../window/new_hide.md).

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the new window's refnum. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | User-visible refnum of the newly created window. |

## Timing and effects

The window has been registered, its initial state and scrollback initialized, and its visible or hidden placement established before the hook runs. Its initial server association is inherited from the current window, or is absent when there was no current window. This hook does not mean the window has joined a channel or completed an IRC connection.

Use `$0` explicitly when applying defaults. A visible window is selected on its screen before the event, but a newly hidden window need not become current. The caller can also perform more selection and housekeeping after the event returns.

The suppression result is ignored: a `^window_create` handler cannot cancel creation, and this event has no separate built-in output. Failed creation does not trigger it. The current non-full-screen implementation refuses additional windows once a current window exists, so such an attempt does not produce an event.

The hook is not protected against recursive invocation. Creating another window in its handler can invoke it again. Avoid deleting or renumbering the new window while its constructor and caller are still using it; use [defer](../commands/defer.md) for disruptive follow-up operations.

## Example

Give each new window a double status bar, including newly hidden windows:

```text
on -window_create "*" (win) {
    window $win double on;
};
```

The explicit refnum selects the operative window for the setting rather than assuming the new window has input focus. See [window double](../window/double.md).

## Source references

- [source/window.c](../../source/window.c): `new_window` initializes and places the window before invoking the hook; `windowcmd_new` and `windowcmd_new_hide` differ in subsequent selection; `windowcmd_double` changes status-bar count.
- [source/screen.c](../../source/screen.c) and [source/irc.c](../../source/irc.c): screen setup and startup create windows through the same constructor.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
