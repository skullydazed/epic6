# on unknown_command

## Synopsis

```text
/on [<noise>]unknown_command "<pattern>" [(<argument list>)] { <commands> }
```

## Description

An unknown command is a command statement that resolves to neither a built-in command nor an eligible user-created alias. When [dispatch_unknown_commands](../set/dispatch_unknown_commands.md) is off, EPIC passes that statement through this hook. The setting defaults to off.

This hook can support script-provided command completion or custom unknown-command handling. It does not perform completion automatically. Use standard [on](../commands/on.md) matching and noise controls; the pattern matches the command and its arguments together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Unknown command name, uppercased, with a `//` prefix when built-in-only lookup was requested. |
| `$1-` | Arguments to the unknown command, possibly empty. |

The parser expands the command statement before lookup when expansion is enabled for that execution context. These arguments therefore describe the parsed command, not necessarily the exact text typed.

Two leading slashes request built-in-only execution, bypassing a same-named alias. If no built-in exists, the hook receives `//COMMAND`, even if an alias with that name exists. Otherwise the leading command slash is omitted. Interactive text without a command slash is ordinary outgoing text, rather than an unknown command statement.

## Default behavior

If the hook permits default handling, EPIC displays `Unknown command: COMMAND`. A suppressing hook, such as a serial-zero `^unknown_command` hook, prevents that diagnostic. The hook does not automatically execute a replacement or retry lookup; a handler must explicitly execute any command it chooses.

When `dispatch_unknown_commands` is on, EPIC skips this hook and forwards the uppercase command and arguments to the current server. Server support and replies then determine the result. The hook is for local command lookup failures, not server numeric unknown-command replies.

The hook is nonrecursive: an unknown command executed from within its handler does not invoke the same hook again. Keep replacement execution explicit and bounded.

## Example

Replace the default local diagnostic:

```text
on ^unknown_command "*" (cmd, args) {
    echo No local command named $cmd was found.;
};
```

This example requires `dispatch_unknown_commands` to remain off.

## History

This hook first appeared in EPIC5-0.3.2.

## Source references

- [source/commands.c](../../source/commands.c): `parse_statement` expands statements, resolves aliases and built-ins, forwards unknown commands when configured, or invokes this hook.
- [source/hook.c](../../source/hook.c): event registration marks the hook nonrecursive and implements matching and suppression.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): `dispatch_unknown_commands` registration and default.
