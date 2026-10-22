# on ping

## Synopsis

```text
/on [<noise>]ping "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for an incoming IRC `PING` with at least one argument, before EPIC sends its automatic response.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender prefix; an absent prefix contributes an empty field. |
| `$1-` | PING arguments joined with spaces. |

## Behavior

If default handling is allowed, EPIC sends `PONG` followed by the joined arguments. A suppressing serial-zero handler prevents that automatic response and can therefore cause the server to disconnect the client unless the script supplies a response. There is no default local display.

The event data is space-separated text: when the sender is absent, the first PING argument becomes the first word visible to positional argument expansion. Use `$*` when observing the complete data.

## Example

```text
on -ping "*" {
    echo Incoming PING: $*;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_ping` joins arguments and conditions its PONG send on the hook result.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
