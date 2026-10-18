# set dispatch_unknown_commands

## Synopsis

```text
/set dispatch_unknown_commands [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings. The registered setting name is plural `dispatch_unknown_commands`.

## Description

When on, command statements that resolve to neither a user alias nor a built-in command are forwarded to the current server context. EPIC uppercases the command name and sends it followed by its parsed arguments. This permits commands supported by an IRC server even when the client has no local implementation.

Whether the server recognizes the command, permits it, or returns an error depends on that server. The setting does not provide a local implementation or guarantee successful execution. It also means a misspelled local command can be transmitted to the server rather than producing a local unknown-command message.

When off, EPIC instead invokes [on unknown_command](../on/unknown_command.md). If that hook permits default handling, it displays `Unknown command: COMMAND`. When forwarding is on, that hook and local diagnostic are skipped.

## Command lookup

Existing aliases and built-ins keep their normal precedence. Two leading slashes request built-in-only lookup, bypassing aliases. If no built-in exists, forwarding can still occur, even if a same-named alias exists. The leading slashes are not sent to the server.

The decision happens after normal command-statement expansion. Arguments are not automatically quoted or converted into an IRC trailing parameter by this feature; they must already have the syntax the server expects. Interactive text without a leading command slash uses ordinary message sending instead of this command lookup path. Script command statements can also reach the forwarding path.

This global setting does not choose a server or establish a connection; forwarding uses the statement's existing server context and normal server-send handling. Merely changing the setting sends no request.

## Example

Enable server forwarding for unknown local commands:

```text
/set dispatch_unknown_commands on
```

Then `/some_server_extension arguments` is forwarded only if no eligible local command handles that name. This is illustrative syntax; support for any particular extension depends on the server.

Return to local hook/error handling with:

```text
/set dispatch_unknown_commands off
```

## Source references

- [source/commands.c](../../source/commands.c): `parse_statement` expands the statement, uppercases the command, resolves aliases and built-ins, and chooses forwarding or `unknown_command` handling.
- [source/server.c](../../source/server.c): normal server-send handling delivers the forwarded protocol line.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and default off.
