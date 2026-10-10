# alias command

## Synopsis

```text
/alias
/alias <name-or-prefix>
/alias -<name>
/alias <name> [(<argument-list>)] {<block>}
/alias <name> <single-command-body>
```

## Summary

Aliases are commands and functions that you create to complement EPIC's built-ins. They can call built-in commands, other aliases, or evaluate expressions. They may return a value when called as `$name(arguments)`.

Defining an alias stores its body for later execution; it does not run the body. Aliases are local to the client and require no server connection. The command is available in every build.

## Forms

With no arguments, `alias` displays your aliases. With only a name or prefix, it lists matching aliases, case-insensitively. Dotted descendants can be grouped as `<Structure>` entries rather than displayed individually; use a more specific prefix to inspect them.

With only `-name`, it removes that exact alias, not every alias sharing the prefix. `/alias -` reports that a name is required. A name that is not present in the symbol table produces `No such alias`. Removing an alias does not remove a built-in command of the same name.

With a name and body, it creates or replaces an alias, including its optional argument list, and normally prints a confirmation. Outer braces are removed from the stored body. Braces are optional for a simple body, but use them for a block containing multiple commands.

The old `/alias /s` diagnostic reports `Sorry, this EPIC does not have caches`; it does not list or modify aliases. The check also matches names beginning with `/s`, case-insensitively.

## Alias names

Names are case-insensitive. This command takes the name as a whitespace-delimited token, so use simple names such as `showsum` rather than names containing spaces or syntax punctuation.

Square-bracket notation is converted to dot notation: `foo[bar][baz]` becomes `foo.bar.baz`.

Avoid beginning names with `(`, `{`, or `@`, because those characters select other kinds of statements. A leading `-` conflicts with the deletion form when no body is supplied. Other punctuation may also make a name difficult to invoke as a function.

## Arguments

Without an argument list, `$*` contains the supplied argument text, and positional expandos such as `$0` and `$1` refer to its words.

An optional parenthesized argument list assigns arguments to local variables. For example, `(first, second, ...)` consumes one word for each named variable and leaves the remaining text in `$*`. Missing values become empty unless a default is supplied. Without `...` or `void`, the final named variable receives all remaining text. `void` discards the remaining arguments; `(void)` discards all of them.

Each named entry can have modifiers such as `default "fallback"`, `words 2`, `uwords 2`, `qwords 2`, or `dwords 2`. `uwords` uses whitespace-separated words; `qwords` groups double-quoted text and preserves its quotes; `dwords` groups double-quoted text and removes its quotes. `words` follows the client's `dword` debugging mode. Counts apply to entries consumed before the final catch-all variable. Default text is expanded when the alias is called. See [argument lists](../arglist.md) for the full word-parsing rules.

Malformed parentheses, braces, missing bodies after an argument list, and text after a closing brace produce diagnostics. These checks do not reliably cancel registration: the handler can still store the remaining or truncated text. Correct the definition and inspect it with `/alias name` before using it.

## Local variables and return value

Each alias call creates a local scope. Local variables, including variables populated from its argument list, are destroyed when the call finishes.

When called as a function, an alias receives an initially empty local `function_return`. Assign to it or use [`return`](return.md) with a value to set the function result. If no value is set, the result is empty. `return` also stops the current alias call.

When called as a command, an alias does not initialize or return its own `function_return` value. Do not assume that assigning this special variable in a command alias is isolated from an enclosing function call. Excessive recursive alias calls are rejected with a recursion diagnostic.

## Aliases and built-ins

Aliases take precedence over built-in commands. To call a built-in command after defining an alias with the same name, use two slashes:

```text
//lusers $*
```

For functions, an ordinary `$name(arguments)` call prefers a built-in function when one exists. Use one colon to force the user alias, or two to force the built-in function:

```text
$:name(arguments)
$::name(arguments)
```

A function with neither a matching alias nor a built-in returns an empty string. Forced calls also return an empty string if the requested kind is absent.

## Examples

To wrap a command and call the built-in version from the wrapper:

```text
alias lusers {
    echo -- User Information
    //lusers $*
    echo -- End User Information
}
```

To create a function that returns the sum of two inputs, then a command that uses it:

```text
alias sum {
    @ function_return = [$0] + [$1]
}
alias showsum {
    echo The sum of $0 and $1 is $sum($0 $1)
}
```

Typing `/showsum 4 5` displays `The sum of 4 and 5 is 9`.

To use named arguments and a default:

```text
alias greet (name default "friend", void) {
    echo Hello, $name
}
```

Typing `/greet` displays `Hello, friend`. Inspect it with `/alias greet`, and remove it with `/alias -greet`.

## History

The `alias` command first appeared in ircII.

## Source

See [source/alias.c](../../source/alias.c): `aliascmd()`, `add_cmd_alias()`, `delete_cmd_alias()`, `list_cmd_alias()`, `parse_arglist()`, `prepare_alias_call()`, and the command/function lookup helpers. [source/commands.c](../../source/commands.c) implements command dispatch, alias scopes, function return initialization, and `return`; [source/functions.c](../../source/functions.c) implements function dispatch and colon prefixes. [source/ircaux.c](../../source/ircaux.c) implements name canonicalization in `remove_brackets()`.
