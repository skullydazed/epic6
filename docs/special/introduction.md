# Crash course in IRC with EPIC

EPIC6 is a terminal IRC client descended from ircII. It provides interactive chat, multiple server connections and windows, configurable keybindings, and a scripting language. This introduction covers the basic commands needed to get started; see [about EPIC](about_epic.md) for background and [glossary](glossary.md) for terminology.

When typing interactively, prefix a command with `/` to distinguish it from conversation text. Ordinary text goes to the current window's target: its query target takes precedence over its channel. Commands in script bodies normally omit this interactive prefix. Names of EPIC commands are case-insensitive.

## Basic commands

Replace placeholders such as `<channel>` with real values; do not type the angle brackets.

| Command | Purpose |
| --- | --- |
| [`/server <server-description>`](../commands/server.md) | Connect to or switch to an IRC server. With no argument, list configured servers. |
| [`/nick <nickname>`](../commands/nick.md) | Request a nickname change on the current server. With no argument, show the current nickname and any pending change. |
| [`/join <channel>`](../commands/join.md) | Request to join a channel. Channel names commonly begin with `#`; other prefixes depend on the server's advertised channel types. |
| [`/part <channel>`](../commands/part.md) | Request to leave a channel. |
| [`/msg <nickname> <message>`](../commands/msg.md) | Send a private message without changing the current conversation target. A channel name can also be used as the destination. |
| [`/query <nickname>`](../commands/query.md) | Make that target the destination for ordinary input in the current window. This does not send a message or open a window by itself. |
| [`/whois <nickname>`](../commands/whois.md) | Request information about a nickname. What is returned, including visible channels and host information, depends on the server and its privacy rules. |
| [`/names <channel>`](../commands/names.md) | Request a channel's visible nickname list. |
| [`/list`](../commands/list.md) | Request a channel listing. Results can be large, and filtering or visibility restrictions depend on the server. |
| [`/quit [message]`](../commands/quit.md) | Disconnect from all servers and exit EPIC. The message is optional; omit the square brackets when supplying one. |

Most commands in this table send requests rather than guaranteeing success. The server can reject a nickname, deny channel entry, limit information, or refuse a message. Read the reply before assuming the action completed. Selecting a query is local client behavior; it does not make a recipient available or establish an encrypted private channel.

## A first conversation

Connect using the server details supplied by your chosen IRC network. The following server name is a placeholder, not a working service:

```irc
/server irc.example.net:PORT=6697:TYPE=IRC-SSL:NICK=YourNick
/join #example
```

This server description requests a TLS connection on port 6697 and supplies an initial nickname. Use the host, port, connection type, and channel appropriate to the network. EPIC does not supply a universal default server list, and some networks require additional authentication or registration steps.

Once the server confirms the connection and channel entry, type a message and press Enter. Use `/msg OtherNick hello` for a message to a particular nickname, or `/query OtherNick` to direct subsequent ordinary input there. To return to the channel when query targets remain, `/window -query` clears the current window's query list. See [query](../commands/query.md) for how selecting and removing individual query targets works.

## Learning more

Use the command links above and the [commands overview](commands_overview.md) to find documentation. The former in-client help browser is unavailable; the retained [help script](../disambiguation/scripts_help.md) depends on a connection interface that does not currently retrieve pages. `/help` is therefore not a dependable entry point for documentation in EPIC6.

For editing keys, see [default keybindings](default_keybindings.md) and [input handling](input_handling.md). For customization, see [programming](programming.md), [functions](functions_overview.md), and [expressions](expressions.md). Scripts can change bindings and command behavior, so examples describe the built-in interfaces unless stated otherwise.

IRC networks have their own rules and support resources. Consult the network's published guidance for connection addresses, authentication, nickname policy, and help-channel names.

## Source references

- `source/commands.c`: interactive parsing, command registration, join/nick/message handling, and WHOIS requests.
- `source/server.c`: server-description parsing and connection management.
- `source/parse.c`: server-advertised channel prefixes and incoming replies.
- `source/window.c`: window query/channel target selection.
- `source/keys.c` and `source/input.c`: keybindings and input submission.
- `script/help.irc` and `source/functions.c`: retained help alias and unavailable connection dependency.
