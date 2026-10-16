# dcc get (removed)

## Former synopsis

```text
/dcc get [=]<nick> [<file> ...] [/directory]
```

## Description

DCC Get formerly accepted a file-transfer offer made by another client with DCC Send and opened a direct connection to download the file. Its filename selection, all-offers form (`*`), alternate local filename, destination-directory argument, and `=nick` form for offers received through DCC Chat belonged to the removed DCC implementation.

EPIC6 has removed `/dcc` and its associated implementation. It cannot accept DCC offers or download files through `/dcc get`. None of the former filename-handling, destination-directory, or direct-connection behavior applies in EPIC6.

In an unmodified session, `/dcc get ...` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script. Retained scripts that call removed DCC primitives do not restore those primitives by themselves.

See [dcc chat](ctcp_chat.md) and [dcc close](ctcp_close.md) for related removed functionality. To receive a file, arrange a transfer through a separate supported tool or service.

## Former examples

These commands identify obsolete configurations and are no longer supported:

```text
/dcc get bob
/dcc get DCCbot *
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP initialization has no DCC file-offer handler.
