# on dcc_connect (removed)

## Former synopsis

```text
/on [<noise>]dcc_connect "<pattern>" { <commands> }
```

## Description

The `dcc_connect` event formerly reported successful establishment of a direct connection with another client, such as [DCC Chat](ctcp_chat.md) or a [DCC Send](ctcp_send.md) file transfer. Allowing default handling displayed a connection message.

EPIC6 has removed the built-in DCC implementation and this named hook. It cannot establish those connections or generate this event through the former implementation. There is no current connection message to customize or suppress.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Remote client's nickname. |
| `$1` | DCC connection type. |
| `$2` | Remote IP address. |
| `$3` | Remote port. |
| `$4` | File name for a file transfer. |
| `$5` | File size for a file transfer. |

These fields describe the removed interface, not arguments supplied by EPIC6. The former paired `send` and `get` notifications for a file-transfer connection are unavailable too.

## Compatibility

Attempting to register `/on dcc_connect ...` is rejected with `No such ON function: DCC_CONNECT`. Retained script references, including the one in `script/builtins`, do not restore the hook or DCC transport. Scripts implementing their own direct connections must provide their own connection notifications and peer metadata.

For successful registration with an IRC server, use [on connect](../on/connect.md). That event concerns a different connection and has different parameters. The former DCC-specific advice about using [userhost](../functions/userhost.md) in this hook does not apply to a current EPIC6 event.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `dccctl`, and related functionality.
- [source/hook.c](../../source/hook.c): the hook table omits `dcc_connect`; `find_hook` rejects unknown named events.
- [include/hook.h](../../include/hook.h): current event identifiers contain no DCC connection event.
- [source/ctcp.c](../../source/ctcp.c): initialization registers no built-in DCC negotiation handler.
- [script/builtins](../../script/builtins): retains a reference to the removed event without implementing it.
