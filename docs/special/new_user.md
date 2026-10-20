# New user information about IRC

This guide introduces IRC rather than listing every EPIC command. Start with [introduction](introduction.md) for the basic commands, then use the [commands overview](commands_overview.md) to explore further.

## What IRC is

IRC stands for Internet Relay Chat. Your client connects to a server on an IRC network. Channels provide shared conversations; messages addressed to a nickname provide direct conversations. Separate networks have separate users, channels, policies, and support resources. People may use different languages and have different expectations about conversation.

EPIC is the client on your machine. Its windows organize local views and message routing; they are not themselves IRC channels or separate connections. Joining a channel and opening or selecting a window are different operations. See [glossary](glossary.md).

## Nicknames and identity

Every connected user has a nickname. Use [nick](../commands/nick.md) to request a change. The server decides whether a requested nickname is valid and available, and a request can be rejected. Nickname registration, reservation, account authentication, and recovery rules depend on the network; EPIC does not establish ownership of a nickname.

A familiar nickname alone does not prove that the person using it is someone you know. [Whois](../commands/whois.md) requests server-provided information, which can help establish context, but the available fields and their meaning depend on server policy. A client-supplied realname is not proof of identity, and a visible host may be masked. Use the network's documented account and identity mechanisms when those distinctions matter.

## Commands suggested by other people

Read an unfamiliar command before running it. Commands can change settings, install persistent hooks, send information to IRC, load scripts, or start local processes. In particular, [exec](../commands/exec.md) runs programs on your machine, [on](../commands/on.md) defines event handlers, and [load](../commands/load.md) executes a script. Check the command's documentation and any script involved rather than treating a pasted command as ordinary chat text.

If you need assistance, use the network's published support resources or ask someone you trust to explain the complete command. Help-channel names vary by network. The former in-client help browser is not a reliable documentation entry point in this version; use the linked documentation pages.

## Command abbreviations

The C command dispatcher looks up complete command names. The bundled `ambig` script adds unique-prefix command expansion through `on unknown_command`; the usual [global](../scripts/global.md) script loads it. With that script active, an abbreviation can run a longer command when there is exactly one matching built-in or alias. Multiple matches produce an ambiguity message.

The available aliases and scripts affect which prefixes are unique. The `dispatch_unknown_commands` setting can also send unknown commands directly to the IRC server instead of invoking the script's hook. Prefer full command names when following instructions or writing startup scripts.

For example, `/names #example` states the intended command explicitly. Do not assume `/na` will always mean the same thing in every configuration. Compatibility aliases such as `signoff` come from scripts, and an abbreviated command can have effects as substantial as disconnecting the client.

## Next steps

- Read [etiquette](etiquette.md) for basic conversation practices.
- Follow [introduction](introduction.md) to connect, join, send messages, and quit.
- Use [input handling](input_handling.md) and [default keybindings](default_keybindings.md) for editing and keyboard behavior.

Commands requesting server actions are not guarantees of success. Read server replies, and consult your network's instructions for authentication, permitted channel use, and connection details.

## Source references

- `source/commands.c` and `source/alias.c`: interactive dispatch, complete-name lookup, aliases, and unknown-command handling.
- `source/server.c`: nickname-change requests and connection state.
- `source/exec.c` and `source/hook.c`: process execution and event handlers.
- `script/ambig`: script-provided unique-prefix command handling.
- `script/global` and `script/builtins`: standard script loading and compatibility aliases.
