# dcc raw (removed)

## Former synopsis

```text
/dcc raw <fd> <host> <message>
```

## Description

DCC Raw formerly sent text through a DCC-managed raw socket connection. The file descriptor and host identified a connection established through the former `$connect()` or `$listen()` facilities and reported by DCC events. `/msg =<fd> <message>` was a shorthand for that transport.

EPIC6 has removed `/dcc` and its associated implementation. Neither `/dcc raw` nor the historical `/msg =<fd>` spelling provides a DCC raw socket transport. The former `dcc_raw` and `dcc_connect` events are not available as a way to obtain such connection identifiers.

In an unmodified session, `/dcc raw ...` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script. Retained scripts that call removed DCC primitives do not restore them by themselves.

The current `$connect()` and `$listen()` function entries return an empty string through disabled implementation branches; they do not create the raw DCC sockets described here. Do not confuse `$connect()` with the IRC [connect](../commands/connect.md) command, which has a different purpose.

## Former examples

These commands identify obsolete raw-socket usage and are no longer supported as DCC sends:

```text
/dcc raw 6 blah.foo.com This is a sample message.
/msg =6 This is a sample message.
```

See [dcc close](ctcp_close.md) for the removed connection-closing operation.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler and the current sending path has no DCC raw transport.
- [source/functions.c](../../source/functions.c): `function_connect` and `function_listen` retain disabled DCC code and return an empty string in the active branches.
- [source/hook.c](../../source/hook.c): event registration omits former DCC events.
