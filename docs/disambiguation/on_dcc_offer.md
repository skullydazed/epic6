# on dcc_offer (removed)

## Former synopsis

```text
/on [<noise>]dcc_offer "<pattern>" { <commands> }
```

## Description

The `dcc_offer` event formerly ran when you offered another person a direct [DCC Chat](ctcp_chat.md) connection or [DCC Send](ctcp_send.md) file transfer. Allowing default handling displayed an offer message. It described an outgoing offer, not acceptance of a connection or receipt of someone else's offer.

EPIC6 has removed the built-in DCC implementation and this named hook. It cannot create those offers through the former commands, and there is no built-in offer notification to customize or suppress.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Person receiving the offer. |
| `$1` | DCC type being offered. |
| `$2` | Offered filename for type `send`. |
| `$3-` | Offered file size for type `send`. |

These describe the removed interface, not arguments supplied by EPIC6.

## Compatibility

Attempting to register `/on dcc_offer ...` is rejected with `No such ON function: DCC_OFFER`. Retained script references, including the one in `script/builtins`, do not restore the event or DCC offers. Scripts implementing their own transfers must provide their own offer notifications.

The former example that matched channel destinations to warn about outgoing offers cannot run through this hook. Sending a DCC-shaped payload with the generic [ctcp](../commands/ctcp.md) command does not recreate the removed DCC connection machinery or generate `dcc_offer`.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `dccctl`, and related functionality.
- [source/hook.c](../../source/hook.c): the hook table omits `dcc_offer`; `find_hook` rejects unknown named events.
- [include/hook.h](../../include/hook.h): current event identifiers contain no DCC offer event.
- [source/commands.c](../../source/commands.c): the command table omits `dcc`; `ctcpcmd` sends generic CTCP payloads without a DCC offer event.
- [source/ctcp.c](../../source/ctcp.c): initialization registers no built-in DCC negotiation handler.
- [script/builtins](../../script/builtins): retains a reference to the removed event without implementing it.
