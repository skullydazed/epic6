# dcc close (removed)

## Former synopsis

```text
/dcc close [<type>|-all|*] [<nick>|-all|*] ["<filename>"]
```

## Description

DCC Close formerly cancelled a pending DCC offer or closed an existing DCC session. It could terminate a chat, stop a file transfer, reject an incoming offer, or close a DCC-managed raw socket or listener.

EPIC6 has removed `/dcc` and its associated implementation. None of the former connection types (`chat`, `send`, `get`, or `raw`), `-all`/`*` selectors, or filename matching forms are supported by this command. It cannot send a DCC rejection notification or close a DCC session in EPIC6.

In an unmodified session, `/dcc close ...` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script. Retained scripts that call removed DCC primitives do not restore those primitives by themselves.

See [dcc chat](ctcp_chat.md) for the removal of direct chat connections. To end an ordinary IRC query, use [query](../commands/query.md); that changes a conversation target and does not close a direct socket or disconnect the IRC server.

## Former examples

These commands identify obsolete configurations and are no longer supported:

```text
/dcc close chat bob
/dcc close get bob
/dcc close chat *
/dcc close -all bob
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP initialization has no DCC negotiation or rejection handler.
