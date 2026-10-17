# on dcc_request (removed)

## Former synopsis

```text
/on [<noise>]dcc_request "<pattern>" { <commands> }
```

## Description

The `dcc_request` event formerly reported incoming offers of [DCC Chat](ctcp_chat.md) or [DCC Send](ctcp_send.md). Allowing default handling displayed a request message. It described an incoming offer, not an established connection or an offer you sent to someone else.

EPIC6 has removed the DCC implementation and this named hook. It no longer parses DCC negotiation into this event or supplies a built-in offer message to customize or suppress.

## Former documented parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Remote client's nickname. |
| `$1` | DCC connection type. |
| `$2` | Connection description. |
| `$3` | Remote IP address. |
| `$4` | Remote port. |
| `$5` | Filename for type `send`. |
| `$6` | File size for type `send`. |

These describe the historical interface, not arguments supplied by EPIC6. No current DCC parser exists to validate or populate these fields.

## Compatibility

Attempting to register `/on dcc_request ...` is rejected with `No such ON function: DCC_REQUEST`. Retained references in scripts such as `autoget` and `builtins` do not restore the hook, automatic acceptance, or DCC transfer machinery. The former example that formatted a request's peer address and file details is unavailable through this event.

An incoming CTCP payload whose command is DCC can still reach the generic [on ctcp_request](../on/ctcp_request.md) hook, subject to its parser limits. That event exposes the CTCP command and argument text; it does not parse these former DCC fields or establish a direct connection. A script implementing DCC negotiation would have to supply that functionality itself.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `dccctl`, and related functionality.
- [source/hook.c](../../source/hook.c): the hook table omits `dcc_request`; `find_hook` rejects unknown named events.
- [include/hook.h](../../include/hook.h): current event identifiers contain no DCC request event.
- [source/ctcp.c](../../source/ctcp.c): `init_ctcp` registers no DCC handler; `do_ctcp` retains generic request handling.
- [script/autoget](../../script/autoget) and [script/builtins](../../script/builtins): retain hook references that depend on removed DCC functionality.
