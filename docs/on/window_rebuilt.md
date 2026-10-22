# on window_rebuilt

## Synopsis

```text
/on [<noise>]window_rebuilt "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered after EPIC rebuilds a window’s scrollback, such as when changed columns require rewrapping its stored output.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Rebuilt window’s user-visible refnum. |

## Behavior

EPIC saves window positions, flushes and reconstitutes scrollback, restores positions, and clears the rebuild flag before the hook. It runs in that window’s server and output context at the `OTHER` level. This is a scrollback-rebuild notification, not a notification for every screen repaint.

The suppression result is ignored and there is no default message. The hook has no nonrecursion protection.

## Example

```text
on -window_rebuilt "*" {
    echo Rebuilt scrollback for window $0;
};
```

## Source references

- [source/window.c](../../source/window.c): `rebuild_scrollback` reconstructs scrollback and invokes the hook; `window_check_columns` requests rebuilds after column changes.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
