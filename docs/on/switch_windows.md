# on switch_windows

## Synopsis

```text
/on [<noise>]switch_windows "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the client changes what it considers the current window through its normal window-selection routine. This includes changes made by window commands and key bindings, screen input, and some temporary command contexts. It is not exclusively a report of keyboard focus changes.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches all four numbers together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Old screen number. |
| `$1` | Old window's user-visible refnum. |
| `$2` | New screen number. |
| `$3` | New window's user-visible refnum. |

A screen number of `-1` indicates a hidden window or an unavailable old screen. Window numbers are refnums, not positions within a screen. Do not assume the old window still exists, particularly during window destruction.

## Effects and limitations

The global current-window selection has already changed before the hook runs. Its suppression result is ignored: it cannot cancel the change, and the event has no separate built-in output. Selecting the same current window again does not generate another event.

Window changes can occur through forces other than the window key bindings. Different old and new screen numbers show a cross-screen change, but equal screen numbers do not prove a key binding caused it. Some internal operations also set the current window informally without this hook; for example, status expando evaluation temporarily selects a window that way. This is therefore not a notification for every internal assignment of window context.

The event does not itself join, part, or switch channels. Each window retains its own channel and query selection; see [on switch_channels](switch_channels.md) for changes to a window's current channel. Changing the current window does not itself send an IRC request.

## Example

Observe the old and new window contexts:

```text
on -switch_windows "*" (oldscreen, oldwin, newscreen, newwin) {
    echo Window context: $oldscreen/$oldwin -> $newscreen/$newwin;
};
```

## Source references

- [source/window.c](../../source/window.c): `make_window_current_by_refnum` changes the global selection and invokes the hook; `make_window_current_informally` changes it without this event; window commands call these selection routines.
- [source/screen.c](../../source/screen.c): screen input selects its current window.
- [source/status.c](../../source/status.c): status expando evaluation temporarily selects windows informally.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
