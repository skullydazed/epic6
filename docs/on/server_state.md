# on server_state

## Synopsis

```text
/on [<noise>]server_state "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC assigns a server connection state through its state setter, including assignments that leave the state unchanged.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum. |
| `$1` | Previous uppercase state name, or `UNKNOWN` for an invalid recorded state. |
| `$2` | New uppercase state name. |

## Behavior

The stored state changes before this hook. [On server_status](server_status.md) follows with the same captured transition, then EPIC requests status updates. Both suppression results are ignored: they cannot cancel the assignment or subsequent connection work. Neither hook has nonrecursion protection.

States are `CREATED`, `RECONNECT`, `POLICY`, `DNS`, `CONNECTING`, `SSL_CONNECTING`, `REGISTERING`, `SYNCING`, `ACTIVE`, `EOF`, `ERROR`, `CLOSING`, `CLOSED`, and `DELETED`. Initial construction assigns `CREATED` directly without this hook. See `server_status` for state meanings. A handler can cause nested transitions, so use the event’s captured arguments to identify the reported assignment.

## Example

```text
on -server_state "*" {
    echo Server $0: $1 -> $2;
};
```

## Source references

- [source/server.c](../../source/server.c): `set_server_state` assigns state and invokes both hooks; `server_states` defines names.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
