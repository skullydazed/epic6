# on window_notified

## Synopsis

```text
/on [<noise>]window_notified "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when output marks a hidden window as notified for the first time since its notification flag was cleared.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Hidden window’s user-visible refnum. |
| `$1` | Message level name of the output that triggered notification. |

## Behavior

The output has already been logged and added to scrollback. The window must be hidden, window notifications must be enabled for this output, its notified flag must be clear, and the message level must match its notify mask. EPIC sets the notified flag before invoking the hook.

The hook runs with output directed to the current window, not the hidden window. Its suppression result is ignored: it cannot prevent the flag, status update, or subsequent built-in activity message. That message depends separately on [window notify](../window/notify.md); beeps are handled separately too. This hook has nonrecursion protection.

## Example

```text
on -window_notified "*" {
    echo Window $0 was notified at level $1;
};
```

## Source references

- [source/screen.c](../../source/screen.c): `add_to_window` applies hidden-window notification conditions, sets the flag, and invokes the hook.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
