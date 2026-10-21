# Programming in EPIC

EPIC's scripting language combines commands, aliases, event handlers, variables, and expressions. Scripts can customize the interface or automate IRC activity. This short guide introduces the rules most likely to matter when writing your first script; see [ircII syntax](ircii_syntax.md) for statement forms and [functions](functions_overview.md) for available operations.

## Commands and aliases

Commands in script bodies do not need the interactive `/` prefix. A script alias can override a built-in command; use two literal slashes to call the C built-in instead of recursively calling the alias:

```irc
alias mode {
    echo Sending a mode request;
    //mode $*;
};
```

This alias forwards its arguments through the real [mode](../commands/mode.md) handler. It does not guarantee the server accepts the request. `//` is understood directly by EPIC's parser; there is no need to construct it from a command-character variable. Command names are case-insensitive. Ordinary interactive text, unlike script statements, is sent to the current target unless it starts with `/`.

## Blocks and semicolons

A block contains semicolon-separated statements. Put braces around bodies that must be saved and run later, including aliases, hooks, timers, and `parse_command` bindings:

```irc
alias install_example {
    on ^hook "example *" {
        echo Hook text: $*;
        echo Second message;
    };
};
```

After defining this alias, run `/install_example`, then `/hook example hello`. Both messages run when the hook fires. Without the hook body's braces, the semicolon would end the `on` statement, and the next `echo` would run immediately while installing it.

Braces group commands; they do not by themselves create a local-variable scope. Calling an alias normally does. Use `local` or `@ :name = value` for local storage; an ordinary assignment updates a visible local if present, otherwise a global. See [atomic scope](atomic_scope.md) and [lvalues](lval.md).

## Backslashes, expansion, and protection

A backslash protects the next character during an applicable parsing or expansion pass and is then removed. The surrounding parser matters: a backslash protecting a semicolon in a script block is different from ordinary text typed at the interactive prompt.

Balanced plain parentheses and braces protect their contents from the current general expando-expansion pass. Their contents can be evaluated later by the command receiving them. Square brackets are not a universal equivalent: they delimit expandable text operands in expressions and participate in variable-name construction. Double quotes do not universally suppress expansion or protect semicolons at the block-splitting stage.

A command statement is expanded before its command and arguments are separated. An expression statement such as `@ count = count + 1` instead uses expression evaluation. `$$` produces a literal dollar sign for one expansion pass; it is not a promise that the resulting text will survive every later evaluation unchanged. See [expando substitution](expando_substitution.md) and [expressions](expressions.md).

## Script files and physical lines

Prefer the [PF loader](pf_loader.md) for a file written as one normal block:

```irc
/load -pf myscript
```

PF joins physical lines with spaces, removes full-line `#` comments, and requires your semicolons. It does not treat `/* ... */` as C-style comments. Indentation and newlines can make the script readable without changing its intended statement boundaries.

The standard loader has separate legacy line-continuation and semicolon-insertion rules. A trailing backslash continuation is a standard-loader convention, not a universal rule for every script block or PF file. Choose the loader explicitly when the distinction matters.

## Code that is evaluated later

Callback bodies need values from the callback's argument context, rather than from the code that installed them. Braces usually express this more clearly than multiple layers of escaped dollar signs and parentheses:

```irc
alias show_userhost {
    userhost $0 -cmd {
        echo Lookup result: $*;
    };
};
```

`/show_userhost SomeNick` uses the alias's `$0` to choose a nickname, but the braced body receives the [userhost](../commands/userhost.md) callback arguments when it runs. The lookup can use a cache and run synchronously, or send a request and run later when replies arrive. Do not assume callback timing or that the caller's ordinary locals remain visible. Use the command's documented callback arguments or explicit extra-data mechanism.

Script-defined functions are aliases called as `$name(arguments)` or from expression mode. `return value` supplies the result. Arguments are text expanded before the function is called, usually separated by spaces; an arithmetic expression inside the parentheses is not evaluated automatically.

## Practical habits

- Use braces consistently around delayed bodies and semicolons between statements, including nested ones.
- Use meaningful local names and dotted structures for related variables; use [Karll arrays](karll_arrays.md) when separate indexed string storage is needed.
- Comment your code using the selected loader's supported syntax and indent nested bodies.
- Use serial-numbered hooks when independent handlers should coexist, and read [on](../commands/on.md) for matching and suppression rules.
- Prefer the command's documented callback or protocol completion event when waiting for server results; a fixed delay is not proof that a reply has arrived.
- Use [debugging](debugging.md) to inspect intermediate values and execution. Errors do not undo earlier definitions, assignments, or requests.

The C built-ins provide the core language. Bundled scripts such as [builtins](../scripts/builtins.md) add compatibility aliases and helpers; availability depends on what your configuration loads. Server-facing commands remain subject to server support, permissions, and connection state.

## Source references

- `source/commands.c`: alias/built-in dispatch, block parsing, function calls, and loaders.
- `source/expr.c` and `source/expr2.c`: statement splitting, expansion protection, and expressions.
- `source/alias.c`: variables, aliases, argument lists, and scope.
- `source/hook.c`: event registration and serial-numbered selection.
- `source/who.c`: `userhost -cmd` parsing and callback execution.
