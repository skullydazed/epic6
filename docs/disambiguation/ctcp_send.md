# dcc send (removed)

## Former synopsis

```text
/dcc send [=]<nick> [-p <port>] <file> [<file> ...]
```

## Description

DCC Send formerly offered one or more files to another client, which accepted the offer and opened a direct connection before transfer began. The `=nick` form sent the offer through an existing DCC Chat connection, and `-p <port>` selected a listening port.

EPIC6 has removed `/dcc` and its associated implementation. It cannot offer or transfer files through `/dcc send`; the former port option, multiple-file arguments, and DCC Chat destination form are unavailable. The old address-advertisement and listener behavior does not apply to this removed command in EPIC6.

In an unmodified session, `/dcc send ...` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script. Retained scripts that call removed DCC primitives do not restore those primitives by themselves.

See [dcc get](ctcp_get.md) and [dcc chat](ctcp_chat.md) for related removed functionality. To share a file, arrange a transfer through a separate supported tool or service.

## Former examples

These commands identify obsolete configurations and are no longer supported:

```text
/dcc send joebob somefile
/dcc send joebob -p 1234 somefile
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP initialization has no DCC file-offer handler.
