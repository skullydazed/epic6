# Syntax of the ircII language

EPIC's scripting language is organized into [blocks](block.md). A block is a sequence of statements separated by semicolons. A statement can be a command, an expression, a nested block, or a block with an argument list.

## Statement forms

| Form | Meaning |
| --- | --- |
| `command arguments` | Execute a script alias or built-in command. |
| `@ expression` | Evaluate an expression and discard its result. |
| `(expression)` | Evaluate an expression and discard its result. |
| `{ block }` | Execute a nested block. |
| `(arglist) { block }` | Bind the current arguments to local variables, then execute the block with the remaining arguments. |

Whitespace separates a command name from its arguments. Each command interprets its own argument text; there is no universal shell-style quoting or comma-separated argument convention. Command names are case-insensitive. A script alias is preferred over a built-in command of the same name; `//command` bypasses the alias and selects the built-in.

An unknown command is normally offered to [on unknown_command](../on/unknown_command.md), then reported as unknown if not handled. Enabling [dispatch_unknown_commands](../set/dispatch_unknown_commands.md) instead forwards unknown commands to the IRC server. Syntax alone therefore does not establish whether a statement is a local operation or a network request.

### Expressions

Expression statements do not receive a general expando-substitution pass before evaluation. The expression parser evaluates variables, functions, operators, and string operands according to its own rules. To insert an expression's value into text, use `${expression}` instead of a standalone expression statement.

```irc
@ count = 2 + 3
```

This assigns `5` to `count` but does not display `5`. See [expressions](expressions.md) for operators, precedence, and text operands.

### Blocks and argument lists

A block can contain more blocks. Braces group statements but do not by themselves create a new local-variable scope. For example, locals created inside an `if` block belong to the surrounding scope; calling an alias normally creates a new scope. See [atomic scope](atomic_scope.md).

A block with an argument list assigns the current `$*` to local variables before executing its body. Parameters consume arguments according to the [arglist](arglist.md) rules. The body's `$*` is the unconsumed remainder; the enclosing statement's argument string is restored afterward. The parameter locals remain in the surrounding scope rather than disappearing at the closing brace.

```irc
alias demonstrate {
    (first, rest) {
        echo First: $first Rest: $rest;
    };
    echo Original arguments: $*;
}
```

`/demonstrate one two three` prints `First: one Rest: two three`, followed by the original argument string. The final named parameter collects the remainder under the ordinary argument-list rules. Malformed `(arglist) {block}` syntax can fall back to expression parsing and produce expression diagnostics.

## Expando substitution

In script evaluation, command statements undergo [expando substitution](expando_substitution.md) before the command name is separated from its arguments. This permits a variable to supply the command name:

```irc
alias choose_command {
    local action echo;
    $action hello;
}
```

`/choose_command` runs `echo hello`. Expansion applies to the statement as a whole, with the expansion engine's protection rules: balanced plain parentheses and braces preserve their contents for that pass, and a backslash protects the next character. Commands may later evaluate protected arguments or bodies. Substituted values are not automatically rescanned as another expansion pass.

Block statements are not expanded as one large string before execution. Their contained command statements are expanded as they run, while contained expression statements use expression evaluation. This allows a later statement to observe changes made by an earlier one.

## Statement boundaries and interactive input

At the block-splitting stage, semicolons inside balanced parentheses or braces do not end the outer statement. A backslash can protect a semicolon. Double quotes and square brackets alone do not protect semicolons from this stage, so quote characters are not a substitute for understanding block boundaries. Unmatched grouping delimiters can produce diagnostics.

Ordinary interactive input follows a different entry path: text without a leading `/` is a message to the current target, and `/command` is parsed as a single interactive statement without the script block's general expansion pass. Typing a semicolon at the prompt does not automatically execute a second command. Commands such as [eval](../commands/eval.md) can deliberately enter script evaluation. See [input handling](input_handling.md).

## Source references

- `source/commands.c`: `parse_block()`, `parse_statement()`, alias/built-in dispatch, and `runcmds_with_arglist()`.
- `source/expr.c`: `next_statement()`, `expand_alias()`, and `parse_inline()`.
- `source/expr2.c`: expression syntax and evaluation.
- `source/alias.c`: argument-list binding and local-variable scope.
- `source/input.c`: interactive submission through `send_line()`.
