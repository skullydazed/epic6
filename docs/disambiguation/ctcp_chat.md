# dcc chat (removed)

## Former synopsis

```text
/dcc chat <nick> [-p <port>]
```

## Description

DCC Chat formerly offered or accepted a direct connection between two clients so conversation could bypass the IRC network. EPIC6 has removed `/dcc` and its associated implementation; it cannot offer, accept, or maintain a DCC Chat connection through this command.

The former `-p <port>` option, pending-offer retransmission, DCC nickname renaming, and CTCP-over-DCC behavior are unavailable. The historical `/msg =nick ...` syntax does not select a DCC Chat transport in EPIC6. Retained scripts that refer to removed DCC primitives do not restore this functionality by themselves.

In an unmodified session, `/dcc chat ...` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script.

For ordinary IRC conversation, use [msg](../commands/msg.md) with a nickname, or [query](../commands/query.md) to select a window's conversation target. Those messages pass through the IRC network and are subject to its connection and delivery rules; they do not create a direct connection.

## Example

The former offer command is no longer supported:

```text
/dcc chat bob
```

To send Bob an ordinary IRC message instead:

```text
/msg bob Hi there bob!
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler, and the current sending implementation has no DCC Chat transport.
- [source/ctcp.c](../../source/ctcp.c): current CTCP initialization does not register a DCC negotiation handler.
