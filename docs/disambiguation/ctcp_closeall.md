# dcc closeall (removed)

## Former synopsis

```text
/dcc closeall
```

## Description

DCC Closeall formerly closed every open or pending DCC connection and sent applicable CTCP DCC rejection replies to peers. It cleared the client's DCC session list.

EPIC6 has removed `/dcc` and its associated implementation. There is no built-in `closeall` operation, DCC session list to clear, or DCC rejection behavior available through this command. In an unmodified session, `/dcc closeall` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script.

See [dcc close](ctcp_close.md) for the removal of individual-session closing and [dcc chat](ctcp_chat.md) for the removal of direct chat connections. This obsolete operation does not describe how to close IRC server connections or ordinary query targets.

## Former example

This command is no longer supported:

```text
/dcc closeall
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP initialization has no DCC negotiation or rejection handler.
