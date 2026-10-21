# Welcome to EPIC6

EPIC is a terminal IRC client descended from ircII, with an extensive scripting language and a long development history. It supports both interactive IRC and scripts that customize the interface and automate client behavior. EPIC6 continues that work with changing interfaces and features; familiarity with EPIC4 or EPIC5 does not guarantee script compatibility.

## Getting EPIC

EPIC6 is distributed as source. In a source checkout, the usual sequence is:

```sh
./configure
make
make install
```

The intended target is a modern POSIX environment with a C99-or-newer compiler, terminfo, and the libraries checked by configuration. It does not support every Unix system or compiler. See [compile options](compile_opts.md), `configure.ac`, and the installation targets in `Makefile.in`. Choose an installation prefix appropriate for your permissions and system.

Binary packages, their names, and their versions depend on the distribution. Check that a package actually supplies EPIC6. For changes and ongoing development, consult [UPDATES](../../UPDATES) and [KNOWNBUGS](../../KNOWNBUGS); archived EPIC4 or EPIC5 releases describe different client generations.

## Script packs

The initial interface is small, and scripts can add substantial features. Choose a script pack that explicitly supports the client version you run. Read its dependencies and installation instructions rather than assuming a pack written for another EPIC generation will work unchanged.

The default [global startup script](../scripts/global.md) loads support scripts, including [builtins](../scripts/builtins.md) and [history](../scripts/history.md). A personal startup file can replace that default path. Consequently, available aliases, completion, and interface behavior can depend on what the session loaded. For script-specific problems, consult that script's documentation or author.

## Using EPIC

Start with a nickname and a server description:

```sh
epic6 mynick irc.example.net
```

Replace the example hostname with your network's server. This requests registration as `mynick`; the server controls whether that nickname is available and whether registration succeeds. TLS, authentication, channels, and network extensions have their own requirements. See [server descriptions](server_description.md) and the [introduction](introduction.md).

Display startup options with `epic6 -h`, or read [command-line options](command_line.md). For display or typing problems, see [output help](output-help.md), [input handling](input_handling.md), and [graphics characters](graphics_chars.md). Terminal capabilities, fonts, and encoding matter independently of script configuration.

## What is IRC?

IRC connects people through servers organized into networks. You join channels to talk with other users on that network and can send private messages to nicknames. EPIC can manage multiple server connections, but the network still determines nickname ownership, channel rules, supported requests, and permissions. Different server implementations have different extensions; no client-side command guarantees that every server accepts it.

## Why EPIC, and who uses it?

EPIC is for people who enjoy IRC and want to shape their client, from users who prefer the ircII style to scripters building a different interface. Aliases, hooks, windows, key bindings, and functions provide tools for that customization. See [about EPIC](about_epic.md) for more background.

## Help and programming

Start with [new users](new_user.md), the [command overview](commands_overview.md), or the [function overview](functions_overview.md). Individual pages distinguish built-in functionality from features supplied by scripts and behavior controlled by an IRC server.

For scripting, read [programming](programming.md), [ircII syntax](ircii_syntax.md), and [expandos](expandos.md). EPIC has its own parsing and evaluation rules; understanding those rules is more useful than assuming compatibility with another language.

## Repository references

- `source/irc.c`: client identity, startup options, and startup-file selection.
- `configure.ac` and `Makefile.in`: build requirements and installation.
- `script/global`: default support-script loading.
- `source/commands.c`, `source/hook.c`, and `source/alias.c`: commands, hooks, and script definitions.
