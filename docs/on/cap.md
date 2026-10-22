# on cap

## Synopsis

```text
/on [<noise>]cap "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered once per capability token in incoming IRC `CAP LS`, `CAP ACK`, or `CAP NACK` replies. Other CAP subcommands do not trigger this hook.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Sender prefix, or `*` if absent. |
| `$1` | Subcommand: `LS`, `ACK`, or `NACK`. |
| `$2` | Capability name for `LS`; unchanged capability token for `ACK` or `NACK`. |
| `$3-` | Value after `=` for `LS`, or empty when absent. `ACK` and `NACK` supply no separate value field. |

## Behavior

For `LS`, EPIC clears the server’s CAP hold before iterating tokens. Tokens containing `=` are split, and their key/value pair is stored in the server’s 005 data before the hook. `ACK` and `NACK` tokens are not split.

After the `LS` hooks, EPIC waits while the server is open and its CAP hold is set, then sends `CAP END` if still open. A script can manage that hold through [serverctl](../functions/serverctl.md), using `SET <server> CAP_HOLD <value>`. The hook’s suppression result is ignored and there is no default display. See [on authenticate](authenticate.md) for SASL messages.

## Example

```text
on -cap "% LS *" {
    echo Capability $2: $3-;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_cap` handles tokens, CAP hold, and `CAP END`.
- [source/server.c](../../source/server.c): `serverctl` implements `CAP_HOLD`.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
