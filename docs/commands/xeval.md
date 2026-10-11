# xeval command

## Synopsis

```text
/xeval [-server server-description] [-window window-reference] [-noisy] -- commands
/xeval [-server server-description] [-window window-reference] { commands }
```

## Description

`xeval` executes command text like [`eval`](eval.md), with temporary server, current-window, and display settings. At the input prompt, it lets script expandos such as `$N` be evaluated. Inside a script, an unbraced statement can already have been expanded before reaching `xeval`; reparsing that text can turn substituted characters into new script syntax.

Braced command text protects its contents from the outer expansion pass, so variables are expanded when the enclosed commands run. The executed body uses the caller's positional argument context and current local-variable scope, not a new alias scope. Normal parser diagnostics, return, break, and continue behavior apply. Output and side effects come from the executed commands; there is no function return value or automatic rollback.

## Options

| Option | Effect |
| --- | --- |
| `-server description` | Temporarily set the server context to an existing registered server found by description. Invalid or unregistered selections leave the current server context unchanged. It does not create or connect a server. |
| `-window reference` | Temporarily select an existing window as current and create an output context directed to it. Invalid selectors leave the current window unchanged. |
| `-noisy` | Turn ordinary display on before executing the body, counteracting an outer quiet statement prefix. |
| `--` | End option processing so the following text is command code. |

Options are case-insensitive, accept `-` or `/` prefixes, and are matched by first letter. Unknown prefixed tokens are consumed without a diagnostic. Use `--` before code beginning with `/` or `-`, or put the body in braces.

Server and window selections are independent: selecting a window does not automatically select its server. If commands should operate on that window's server, set both to matching references. Output target, sender, and level are inherited when the window output context is created. Without `-window`, `xeval` retains the existing output context.

The handler contains a `-nolog` branch, but its earlier first-letter `-noisy` match takes precedence: **`-nolog` currently behaves as `-noisy` and does not inhibit logging.** Do not use it to keep data out of logs.

## Restoration and restrictions

After normal body execution, the original current window, server context, and display flag are restored. Commands that changed these selections inside the body therefore do not normally leave those selections in place. Other effects, such as variable changes, joins, or opened files, remain.

Use only one `-window` option per invocation. Repeating it creates multiple output contexts while the handler releases only the final one, leaving an earlier context behind. A missing `-server` value returns immediately before normal restoration; earlier window or display changes in that invocation can remain. Supply complete option values rather than relying on missing-argument handling.

The command is always registered and requires no IRC connection unless the body needs one. An empty command body does nothing beyond any selected temporary context changes. Avoid reparsing untrusted incoming strings as code: braces should enclose the fixed commands, with untrusted values expanded as their arguments. This protection is not a general sanitizer for text deliberately concatenated into executable syntax.

## Examples

At the input prompt:

```text
/assign myvar isn't this neat?
/xeval -- echo This string contains a variable: $myvar
```

Execute trusted stored code in a script:

```text
@ mycode = 'echo first;echo second'
xeval -- $mycode
```

Route a received message to an existing window named `MsgWin`, keeping the incoming text inside a fixed braced command:

```text
on msg * {xeval -window MsgWin {xecho -banner Message from $*}}
```

By contrast, `xeval -window MsgWin xecho -banner Message from $*` in that handler allows substituted message text to become new statement syntax. Use [`echo`](echo.md) or another direct command when reparsing is unnecessary. For indirect string expansion without evaluating new statements, see the expression expansion operator `**` described with script expressions.

## Source

[source/commands.c](../../source/commands.c) registers `xeval`, implements `xevalcmd()`, and executes the body through `runcmds()`/`parse_block()`. [source/window.c](../../source/window.c) implements window lookup, selection, and output-context stacking. [source/server.c](../../source/server.c) implements server-description lookup; [source/expr.c](../../source/expr.c) handles statement expansion and brace protection.
