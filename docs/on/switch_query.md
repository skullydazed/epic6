# on switch_query

## Synopsis

```text
/on [<noise>]switch_query "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by the `SWITCH_QUERY` keybinding when it rotates the current window’s query target.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Window’s user-visible refnum. |
| `$1` | Previous query target. |
| `$2` | New query target. |

## Behavior

The keybinding selects an older query from the window’s query list, updates its query counter, and requests a status-bar update before invoking the hook. It returns without an event when it cannot select a different query. This event is specific to this keybinding path; it is not a general notification for every query-list change.

The suppression result is ignored. There is no separate default message and suppressing the hook cannot undo the switch. See [bind](../commands/bind.md) and [query](../commands/query.md).

## Example

```text
on -switch_query "*" {
    echo Window $0 query: $1 -> $2;
};
```

## Source references

- [source/window.c](../../source/window.c): `switch_query` selects and promotes the target before invoking the hook.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
