# on reconnect_required

## Synopsis

```text
/on [<noise>]reconnect_required "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered on certain connection failures involving a previously registered server, before EPIC closes the connection.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum requiring recovery. |

## Behavior

The read-failure path marks the server unregistered before this hook, then closes it afterward. Earlier automatic protocol-retry branches can bypass the hook. A failed write also triggers it for a registered server when [set no_fail_disconnect](../set/no_fail_disconnect.md) is off; that path assigns `ERROR` before the hook and closes afterward.

The suppression result is ignored and does not prevent closure or initiate a reconnect. Schedule disruptive recovery work with [defer](../commands/defer.md) so it runs after the failure path finishes. This is not a notification for every intentional disconnect; see also [on server_lost](server_lost.md).

## Example

```text
on -reconnect_required "*" {
    echo Server $0 needs connection recovery;
};
```

## Source references

- [source/server.c](../../source/server.c): `server_io` and `send_to_aserver_raw` invoke the hook before closure.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
