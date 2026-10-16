# dcc rename (removed)

## Former synopsis

```text
/dcc rename [<nick>] <name> [<new-name>]
/dcc rename -chat <dcc-nick> <new-nick>
```

## Description

DCC Rename formerly changed the local filename associated with an offered file, allowing it to be received under another name. Its `-chat` form renamed the local peer identifier for an existing DCC Chat connection; subsequent DCC-targeted commands used that identifier.

EPIC6 has removed `/dcc` and its associated implementation. There is no built-in DCC offer filename to rename or DCC Chat peer identifier to change, and the former `-chat` option is unavailable.

In an unmodified session, `/dcc rename ...` is an unknown client command. A script could define a command named `dcc`, but its behavior would belong to that script. This removed operation does not rename files already on disk or change an IRC nickname in EPIC6.

See [dcc get](ctcp_get.md) and [dcc chat](ctcp_chat.md) for related removed functionality.

## Former examples

These commands identify obsolete configurations and are no longer supported:

```text
/dcc rename joebob foo bar
/dcc rename -chat joebob jimbob
```

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
- [source/commands.c](../../source/commands.c): the command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP initialization has no DCC offer or chat handler.
