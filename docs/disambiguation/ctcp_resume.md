# dcc resume (removed)

## Former synopsis

```text
/dcc resume [=]<nick> <file>
```

## Description

DCC Resume formerly attempted to continue an incomplete DCC Get transfer from the size of a partially downloaded file, negotiating the restart point with the sending client instead of downloading the whole file again.

EPIC6 has removed `/dcc` and its associated implementation. It cannot negotiate or perform resumed DCC transfers through this command. The former `mirc_broken_dcc_resume` setting and compile-time flag are also absent; enabling an old setting or consulting historical build-option letters does not restore the feature.

In an unmodified session, `/dcc resume ...` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script. Retained scripts that call removed DCC primitives do not restore them by themselves.

See [dcc get](ctcp_get.md) for the removed file-receiving operation. To continue an incomplete file transfer, use a separate supported transfer tool whose sender and receiver agree on a resumption protocol.

## Former example

This command is no longer supported:

```text
/dcc resume joebob somebigfile
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): current settings and configuration omit the former DCC resume switch and flag.
