# About EPIC

EPIC is the **Enhanced Programmable IRC-II Client**: a terminal-based IRC client descended from ircII, with an extensive scripting language and a long history of extending and refining that client. The code and copyright history retain contributions from Michael Sandroff, Troy Rollo, Matthew Green, Jake Khuon, EPIC Software Labs, and other contributors.

EPIC6 continues that lineage, but it is not a promise of complete compatibility with every ircII or EPIC5 script. Commands, functions, defaults, and optional integrations change. Some familiar behavior is supplied by scripts rather than by a built-in command, and an older script's presence in the source tree does not guarantee that all of its dependencies remain implemented.

## What EPIC offers

- **Programmability.** Aliases, variables, functions, event hooks, key bindings, timers, and loadable scripts let you adapt input, output, and client behavior. The supplied [builtins](../scripts/builtins.md) and [global](../scripts/global.md) scripts are part of that environment, not just optional decoration.
- **Interactive use and automation.** EPIC supports normal terminal sessions and detached bot operation. The `-b` startup option selects bot mode; scripts remain responsible for the automation they implement. Network policy and permissions still govern what an automated client may do.
- **Protocol access.** High-level commands provide common IRC operations, while [quote](../commands/quote.md) allows a script or user to send protocol text. The server controls supported commands, network extensions, permissions, and accepted requests; client flexibility does not override those rules.
- **Customizable presentation.** Windows, status lines, message routing, hooks, and key bindings provide control over the interface. Completion, history, reconnect behavior, and other conveniences can be implemented or extended by scripts.
- **Source availability.** The repository contains the client and supplied scripts. The [copyright and license](../../COPYRIGHT) permits redistribution in source and binary form, with or without modification, subject to its stated conditions. Individual files can carry their own notices.

## EPIC6 direction

The project's EPIC6 work emphasizes making complicated operations simpler, reducing clutter, and improving features people use. Some facilities have been moved into scripts; others are being redesigned or retired. Consult the documentation for the specific feature rather than treating older command counts, binary-size comparisons, or blanket compatibility claims as a description of the current client.

The client is written in C. Its configure source targets a C99-or-newer compiler and a modern POSIX environment, with terminfo support and the libraries checked during configuration. Optional integrations, such as Python, depend on build configuration. The former focus on simultaneous ANSI and K&R function declarations does not describe the current build requirements.

## Working with the client

The default startup script loads several support scripts. A personal startup configuration can replace that default path, so behavior available in one session may depend on definitions that have not been loaded in another. Start with the documentation for [global startup](../scripts/global.md), [loading scripts](../commands/load.md), and the particular command, function, or setting you want to use.

EPIC's scripting facilities execute local client operations and can invoke external processes when explicitly requested through facilities such as `exec`. Read a script's requirements and effects before relying on it; server-facing actions also depend on the connected network. EPIC6 does not define a universal return value, command syntax, or default for the project itself: those belong to individual features.

## Repository references

- [COPYRIGHT](../../COPYRIGHT): attribution, redistribution conditions, and warranty terms.
- [source/irc.c](../../source/irc.c): EPIC6 identification, startup, and detached bot mode.
- [source/commands.c](../../source/commands.c): command dispatch and protocol-command interfaces.
- [source/alias.c](../../source/alias.c), [source/hook.c](../../source/hook.c), and [source/keys.c](../../source/keys.c): scripting definitions, event hooks, and key bindings.
- [script/global](../../script/global) and [script/builtins](../../script/builtins): supplied startup and support environment.
- [configure.ac](../../configure.ac): supported build environment and dependency checks.
- [KNOWNBUGS](../../KNOWNBUGS): EPIC6 development goals, outstanding work, and feature transitions.
