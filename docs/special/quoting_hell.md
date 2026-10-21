# Quoting hell

## What is quoting hell?

“Quoting hell” describes cases where it is hard to tell which parsing pass will expand a dollar sign, remove a backslash, or recognize a command separator. Code queued for later execution starts as text passed to another command. That text can be expanded while being queued and then parsed again when it runs.

Arguments to functions and script command statements normally undergo [expando substitution](expando_substitution.md), including backslash dequoting. If a value should be read later rather than now, it must survive the earlier pass. Doubling dollar signs or adding backslashes can accomplish this, but the number of passes must be understood. Braced bodies usually make the intended timing clearer.

## Example: reading the clock later

The `$z` expando supplies the client's formatted clock string. In a script statement:

```irc
echo $z;
```

This reads the value when `echo` is evaluated. To print it in two minutes, this is insufficient:

```irc
timer 120 echo $z;
```

The statement expands `$z` before `timer` receives it, so the timer stores an `echo` command containing the earlier clock text. Instead, protect the dollar sign for one pass:

```irc
timer 120 echo $$z;
```

`$$` becomes a single literal `$` during scheduling. The timer later evaluates the stored `echo $z`, reading the clock then. These examples describe script evaluation; ordinary interactive input does not receive the same initial general expansion pass.

## Adding a deferred execution pass

[Defer](../commands/defer.md) queues work for housekeeping at the outermost event-loop level. It can avoid executing that work inside a nested event loop, such as one servicing a blocking wait. It is not a separate timed delay and does not guarantee a particular execution time.

In a script, `timer 120 defer echo $$z` protects the clock only through scheduling. When the timer runs, `$z` is expanded before `defer` stores the text. To leave it for the deferred execution, another level is needed:

```irc
timer 120 defer echo $$$$z;
```

The relevant text changes from `$$$$z` to `$$z` at scheduling, then to `$z` while the timer queues the deferred command, and finally to clock text when the deferred `echo` runs. The `defer` command must remain in the command chain; doubling dollar signs alone does not defer execution.

## Prefer bodies that show their timing

The same intention is clearer with braces:

```irc
timer 120 {
    defer {
        echo $z;
    };
};
```

The timer body is protected while scheduling, and the deferred body is protected while the timer runs. Each command evaluates its saved body when it is ready. This avoids manually counting dollar-sign pairs for this case.

Balanced plain parentheses and braces protect their contents from the current general expansion pass; their later interpretation depends on the receiving command. Square brackets and double quotes are not universal expansion shields. Expression string operands, hook patterns, and individual command argument parsers have their own rules. A backslash protects a character for an applicable pass, not for every later pass automatically.

Protection also does not capture local-variable storage. Timers and deferred commands run in their documented execution contexts, and the scheduling alias's ordinary locals are not preserved for them. Use supported callback arguments, shared storage, or deliberately captured values when that is the intended behavior.

## Finding the extra pass

When a delayed command prints the wrong value or splits unexpectedly, trace the text at each stage: the scheduling statement, the text received by the queuing command, and the later execution. Use [debugging](debugging.md) and small examples to inspect intermediate values. Do not keep adding escapes without identifying which parser consumes them.

The executed commands retain their usual output and errors. Quoting controls timing and syntax; it does not guarantee that a timer target, server connection, or other resource will still exist later. See [programming](programming.md), [timer](../commands/timer.md), [defer](../commands/defer.md), and [ircII syntax](ircii_syntax.md).

## Source references

- `source/expr.c`: expansion, `$$`, backslash dequoting, and protected parentheses/braces.
- `source/commands.c`: statement parsing, deferred-command storage, and deferred execution.
- `source/timer.c`: saved timer text and later lambda-command execution.
- `source/irc.c`: deferred dispatch at the outer event-loop level.
- `source/functions.c`: `$z` clock expando and expanded function arguments.
