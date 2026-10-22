# on context

## Synopsis

```text
/on [<noise>]context "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered whenever EPIC pushes an output context with `set_context`. A context describes output routing and message classification; this event does not guarantee that a line will be displayed.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Context server reference, which may indicate no server. |
| `$1` | Context window reference as supplied by the caller; may be an internal identifier or a routing sentinel, not a user-visible refnum. |
| `$2` | Sender, optionally expanded to `nick!user@host`; `*` when absent. |
| `$3` | Target, optionally expanded to `nick!user@host`; `*` when absent. |
| `$4` | Message level name. |

## Behavior

The context is already pushed when the hook runs. Non-channel senders and targets are expanded when cached userhost information is available; sender expansion can also use the parser’s current userhost. Popping a context does not trigger this event.

The suppression result is ignored. This hook has no nonrecursion protection: output or commands that push another context can invoke it again. Keep handlers minimal and avoid context-producing operations.

## Example

```text
on -context "*" {
    @ last_context = [$*];
};
```

## Source references

- [source/window.c](../../source/window.c): `real_set_context` stores the context, builds sender/target strings, and invokes the hook; `pop_context` removes it.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
