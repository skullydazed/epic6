# on authenticate

## Synopsis

```text
/on [<noise>]authenticate "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for an incoming IRC `AUTHENTICATE` command with at least one argument. This provides a script entry point for SASL authentication; it does not itself perform an authentication exchange.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender prefix, or `*` if absent. |
| `$1-` | Authentication arguments, joined with spaces; commonly `+` or encoded challenge data. |

## Behavior

The hook has no built-in response or display, and its suppression result is ignored. Scripts must send any required protocol response themselves. Capability negotiation is exposed through [on cap](cap.md).

## Example

```text
on -authenticate "*" {
    echo AUTHENTICATE from $0: $1-;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_authenticate` validates and joins arguments before dispatch.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
