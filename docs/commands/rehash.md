# rehash

Ask an IRC server to reload its configuration.

## Synopsis

```text
/rehash [server-specific-arguments]
```

## Description

`rehash` requests a configuration reload on the current IRC server, commonly allowing an administrator to apply configuration changes without restarting the server. Some servers also accept subcommands to reload particular files or perform related administrative tasks. The supported arguments, configuration filenames, and resulting effects depend on the server implementation.

EPIC forwards the entire argument text as `REHASH arguments`, or sends a bare `REHASH` when none is supplied. It does not parse or validate subcommands, supply argument defaults, reload local EPIC scripts, or check your privileges before sending. There are no EPIC-specific options, and the built-in is always registered.

## Restrictions and output

Servers normally restrict this operation to authorized IRC operators, potentially requiring specific privileges. See [oper](oper.md). The server can reject an unsupported or unauthorized request. Successful transmission is not confirmation that a reload completed; any notices, numeric replies, or errors come from the server and pass through ordinary [on](on.md) hooks and display handling. The command has no local success output or result value.

The request needs a usable IRC connection. It uses the current server context, falling back to the primary server when no current context exists. Normal server [encoding](encoding.md), line limits, and the `send_to_server` hook apply.

## Example

Request the server's default reload operation, if you are authorized:

```text
/rehash
```

Consult that server's administrative documentation before supplying additional arguments.

## Source

Registration and the generic `send_comm` handler are in [source/commands.c](../../source/commands.c). Connection selection and transmission are in [source/server.c](../../source/server.c), with ordinary numeric reply handling in [source/numbers.c](../../source/numbers.c).
