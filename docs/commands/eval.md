# eval command

## Synopsis

```text
/eval <command-text>
/eval {<commands>}
```

## Description

`eval` treats its arguments as EPIC script syntax and executes them. Its most common use is at the input prompt: ordinary interactive commands do not expand `$` variables, but `eval` runs its argument text through the script parser so expandos are substituted.

Inside a script, command statements are normally expanded before their handler runs. `eval` then parses and expands the resulting text again. This extra pass can resolve an indirect variable reference, but it also lets text become new command syntax.

For example, in a script:

```text
if (1) {
    @ variable = 'this is some text';
    @ indirect = '$variable';
    echo $indirect;
    eval echo $indirect;
}
```

The two echo commands display:

```text
$variable
this is some text
```

The first pass replaces `$indirect` with the literal text `$variable`. The ordinary `echo` handler prints that text. In `eval echo $indirect`, `eval` receives `echo $variable`, and its execution pass substitutes the variable's value before running `echo`.

## Parsing and scope

There are no options or required arguments beyond the text you want to execute. With no text, it does nothing. It accepts command statements, semicolon-separated blocks, and expression statements such as `@ foo = 4 + 5`; it does not return the expression result as a function value. Any output and effects come from the executed statements.

Braces protect their contents from the outer command-expansion pass. For example, `eval { echo $foo }` evaluates the braced command when that block runs, instead of substituting `$foo` while preparing the `eval` statement. Exact expansion depends on the text's braces and escapes; `eval` is not an unlimited recursive substitution operation.

Execution uses the current server, window/output context, and local-variable scope. It does not create an alias scope, change the current conversation target, or send unprefixed command text as chat. Saved positional arguments such as `$*` are the current caller's arguments, or empty when invoked without a script argument context.

Normal parser errors and control flow apply. A valid `return`, `break`, or `continue` can stop evaluation and propagate to its surrounding call or loop. Braces, parentheses, and command names must obey ordinary script syntax; `eval` does not prevalidate the whole block or undo earlier statements if a later one fails.

The command is available in every build and needs no IRC connection unless the executed text does.

## Using input safely

Do not pass text from a server or another user to `eval` merely to display it. Semicolons and other syntax in that text can become commands on the additional parsing pass. Use [`echo`](echo.md) directly for display.

For example, `eval echo $message` can execute a second command if the expanded message contains `hello; echo another command`. Bracing the original expression is not a general sanitizer for arbitrary text later incorporated into evaluated code.

## Example at the input prompt

To expand the nickname expando:

```text
/eval echo My nickname is $N
```

## See also

[`echo`](echo.md), [`do`](do.md), [`alias`](alias.md), and [script syntax](../ircii_syntax.md).

## Source

See [source/commands.c](../../source/commands.c) for `evalcmd()`, `runcmds()`, `parse_block()`, and `parse_statement()`. [source/expr.c](../../source/expr.c) implements statement boundaries and expansion, including brace protection.
