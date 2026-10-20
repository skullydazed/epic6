# Glossary

Definitions and starting points for EPIC scripting and client concepts. Follow the linked pages for full syntax, options, and limitations.

## Local variables

A local variable stores text in the current script scope. Use `local name value` or an explicitly local expression assignment such as `@ :count = 1` to create one. An ordinary assignment updates an already visible local if there is one; otherwise it can create or update a global variable. Local variables normally disappear when their scope ends. See [local](../commands/local.md) and [atomic scope](atomic_scope.md).

## Atomic scope

An atomic scope defines which local variables are visible and how long they live. Calling an alias ordinarily creates a new scope, hides the caller's ordinary locals, and restores their visibility when the call returns. Plain braces around a block or loop do not create a new scope by themselves.

“Atomic” does not mean uninterrupted execution or an all-or-nothing transaction: waiting operations can process other events while a scope is suspended. See [atomic scope](atomic_scope.md).

## Expression mode

Expression mode interprets text as operands and operators and produces a value. For example, `@ count = 2 + 3` assigns `5`; `${2 + 3}` inserts `5` into expanded text. Variable names normally appear without `$` inside expressions. Operators have precedence and associativity, so parentheses can change grouping. See [expressions](expressions.md).

## Text mode

Text mode treats a statement as a command name followed by text arguments. In script evaluation, `$` forms substitute values; ordinary interactive input does not automatically receive that same general expansion pass. Function parameters also use text mode, with each function interpreting its expanded argument string. See [expando substitution](expando_substitution.md), [blocks](block.md), and [functions](functions_overview.md).

## IPv6 support

IPv6 is an Internet address family supported by EPIC's network and server-connection code alongside IPv4. A server entry's protocol field can select `tcp6` for IPv6 or `tcp4` for IPv4; an unspecified family allows address resolution for either. A successful connection still requires a usable address, operating-system support, routing, and an accessible server. See [server](../commands/server.md) for server-entry syntax.

## New to IRC?

IRC is a networked chat protocol. EPIC is the client running on your machine; an IRC server accepts connections and exchanges messages. A nickname identifies a participant on a network, and a channel is a shared conversation target. A query selects a private-message target in an EPIC window; it does not create a separate network connection.

An EPIC window is a local view and message-routing context, not a channel itself. A window can be associated with channels or a query, and hidden windows can still receive output. Start with [server](../commands/server.md), [join](../commands/join.md), [msg](../commands/msg.md), [query](../commands/query.md), and [window](../commands/window.md). Network policy determines such things as accepted nicknames, channel modes, and server rate limits.

## Karll arrays

Karll arrays are named collections of strings managed by functions such as `setitem()` and `getitem()`. Item numbers start at zero. They are separate from space-separated word lists and ordinary variables with dotted or bracketed names. Some array functions use sorted indices rather than item numbers, so the two must not be interchanged. See [arrays](arrays.md).

## Environment variables

Environment variables are process-level name/value pairs inherited when EPIC starts. Some select startup behavior; others can be read through `getenv()` or used as a fallback during ordinary variable lookup. Script variables and environment variables are separate storage: assigning an EPIC variable does not export it to child processes. Environment names can be case-sensitive even though EPIC script names are case-insensitive. See [environment variables](environment_variables.md) and [setenv](../commands/setenv.md).

## Debugging a script

Debugging means finding why a script's actual behavior differs from its intended behavior. `echo` can show intermediate values; `set debug` enables numeric debug flags, while `xdebug` controls named diagnostics and compatibility flags. These are different interfaces. See [debugging](debugging.md).

## Command-line options

Command-line options configure the EPIC process at launch, before ordinary interactive commands. For example, `-n nickname` supplies a nickname and `-q` skips startup-script loading. They are not commands to type into an already running client. Server descriptions and remaining arguments have their own parsing rules. See [command line](command_line.md) for the supported options and startup precedence.

## Server numerics

A numeric is an IRC reply or error identified by a number, conventionally written as three digits. Its arguments and meaning come from the server protocol and implementation. EPIC can update local state, run hooks, and display a reply when processing it; the number is not an EPIC function's return code.

Scripts can register numeric `on` handlers, and the named `numeric` hook handles the general display stage. Some replies have special processing paths, so do not assume every numeric has identical hook arguments or timing. See [on](../commands/on.md), [on numeric](../on/numeric.md), and [show_numerics](../set/show_numerics.md).

## Source references

- `source/alias.c`: local/global storage and scope visibility.
- `source/expr.c`, `source/expr2.c`, and `source/commands.c`: text/expression evaluation and alias execution.
- `source/array.c`: Karll array storage and indexing.
- `source/network.c` and `source/server.c`: address families and connection setup.
- `source/irc.c`: startup and command-line parsing.
- `source/vars.c` and `source/debug.c`: diagnostic settings and flags.
- `source/parse.c`, `source/numbers.c`, and `source/hook.c`: IRC parsing and numeric events.
