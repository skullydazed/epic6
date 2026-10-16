# dcc list (removed)

## Former synopsis

```text
/dcc list
```

## Description

DCC List formerly displayed active and pending DCC connections, including connection type, peer nickname, status, opening time, and file-transfer details such as size, throughput, and filename.

EPIC6 has removed `/dcc` and its associated implementation. There is no built-in DCC session listing, `dcc_list` event, or `dcc_long_pathnames` setting. The former filename-display behavior and list hook do not apply in EPIC6.

In an unmodified session, `/dcc list` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script. Retained scripts that call removed DCC primitives do not restore those primitives by themselves.

See [dcc chat](ctcp_chat.md), [dcc get](ctcp_get.md), and [dcc close](ctcp_close.md) for related removed functionality.

## Former example

This command is no longer supported:

```text
/dcc list
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler.
- [source/hook.c](../../source/hook.c) and [source/vars.c](../../source/vars.c): event and setting registration omit the former DCC list event and filename-display setting.
