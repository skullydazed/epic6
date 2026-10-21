# Welcome to EPIC6

EPIC is a terminal IRC client descended from ircII, designed for interactive IRC use and scripting. Its aliases, hooks, functions, windows, and loadable scripts let you shape the experience. Start with [about EPIC](about_epic.md) for background or the [introduction](introduction.md) for basic use.

## Getting EPIC6

EPIC6 is distributed as source code. From an EPIC6 source checkout, the usual build and installation sequence is:

```sh
./configure
make
make install
```

Configuration checks the local compiler and libraries. The intended environment is a modern POSIX system with a C99-or-newer compiler and terminfo support, plus the required libraries identified by configuration. Installation writes to the configured prefix; choose one appropriate for your system. See [compile options](compile_opts.md) and the repository's `configure.ac` for build requirements and options. Optional integrations depend on how the client was built.

EPIC6 is an evolving development line. Features, defaults, and script interfaces can differ from EPIC4 or EPIC5; an older package or script pack is not automatically an EPIC6-compatible installation. The repository's [UPDATES](../../UPDATES) and [KNOWNBUGS](../../KNOWNBUGS) describe changes and ongoing work. Distribution package names and availability depend on the operating system and its maintainers.

## Script packs and configuration

The initial interface is intentionally small. Supplied scripts and personal configuration can add completion, history, automatic reconnection, and other behavior. A script pack may provide a substantially different interface, so check its supported client version and dependencies. The presence of an old script in the source tree does not guarantee all its original dependencies remain available.

Read [global startup](../scripts/global.md), [loading scripts](../commands/load.md), and the documentation for the scripts you use. For script-specific behavior, consult that script's author or documentation.

## Using EPIC

Provide a nickname and a server description when starting the client:

```sh
epic6 mynick irc.example.net
```

Replace the example hostname with your network's server. This requests a connection using `mynick`; the server decides whether the nickname and registration are accepted. Network addresses, TLS requirements, authentication, channel rules, and available extensions vary. See [server descriptions](server_description.md) for specifying the connection.

To display command-line options:

```sh
epic6 -h
```

See [command-line options](command_line.md) for details. If typing or display is incorrect, consult [output help](output-help.md), [input handling](input_handling.md), and [graphics characters](graphics_chars.md). Terminal configuration, fonts, and encoding all affect the result.

## What is IRC?

IRC connects people through servers and networks. Users join channels and exchange messages with other people on the same network. EPIC can manage connections to multiple servers, but channel names and nicknames belong to their network context. IRC has multiple server implementations and protocol extensions; support for a particular operation depends on the server and client implementation. See [new users](new_user.md) and [IRC etiquette](etiquette.md).

## Help and programming

These pages document client features and supplied scripts. Useful reference starting points are [commands](commands_overview.md), [functions](functions_overview.md), [key bindings](key_bindings.md), and [special variables](special_vars.md).

To customize or automate the client, begin with [programming](programming.md), [ircII syntax](ircii_syntax.md), and [expando substitution](expando_substitution.md). Scripts execute local client operations as well as server requests, so read their requirements and effects before loading them.

## Repository references

- `source/irc.c`: program identity, startup arguments, and help output.
- `configure.ac` and `Makefile.in`: build checks and installation targets.
- `script/global` and `script/builtins`: supplied startup environment.
- `KNOWNBUGS` and `UPDATES`: development direction and change history.
