# return

Finish the current alias, callback, loaded file, or function block early.

## Synopsis

```text
/return [text]
```

## Description

`return` stops normal processing of the current return-handling call. Supported contexts include an [alias](alias.md) called as a command or function, [defer](defer.md) execution, [exec](exec.md) callbacks, [load](load.md), [on](on.md) handlers, [queue](queue.md) entries, [timer](timer.md) script callbacks, and [wait](wait.md) command callbacks. A `{...}` block used as an expression also establishes a function return context.

Plain command braces, conditionals, and ordinary loops do not establish a new return boundary. A return inside them propagates to the nearest enclosing call or loaded file that handles it. A nested alias or callback handles its own return, so it normally ends that call rather than the outer caller. In a loaded file, return stops that file's processing without abandoning the script that loaded it.

The optional text is the entire remaining argument string. After ordinary command expansion it is assigned to the special local variable `function_return`; `return` does not independently evaluate it as a mathematical expression. To return an evaluated expression, use `${...}` as in the example below.

A function invocation initializes `function_return` to empty and reads it back when the call finishes. A bare return does **not** clear an existing value: it preserves any value already assigned to `function_return`. It returns an empty result only when that result variable remains empty. The same mechanism supplies results from function-expression blocks and serial-zero `on ?event` handlers.

Command calls do not consume a returned result as a value. However, `function_return` has special scope routing to the innermost active function frame. A `return text` from a command alias invoked within a function can therefore change that enclosing function's result even though it ends only the command alias. Use a bare return when you want early command exit without assigning a result.

## Restrictions

Outside an active return-handling context, the command prints `Cannot RETURN here.` and neither stops processing nor assigns a result. It has no options, is always registered, needs no IRC connection, and produces no normal output.

Return propagation depends on the construct executing the body. In particular, [repeat](repeat.md) does not inspect the pending return between repetitions; later repetitions can execute their first statement again before block parsing notices it. Use ordinary loops when you require dependable early loop/function exit.

## Examples

Define a function and return its calculated result:

```text
alias square (arg) {return ${arg * arg}}
eval echo $square(5)
```

This displays `25`.

End a command alias early:

```text
alias maybe_echo {if (!*0) {return}; echo $*}
```

With no arguments, `maybe_echo` returns before displaying anything.

## History and source

`return` first appeared in EPIC4pre1.031.

Registration, `returncmd`, block propagation, function/command callbacks, and loader return handling are in [source/commands.c](../../source/commands.c). Special `function_return` scope lookup is in [source/alias.c](../../source/alias.c), conditional hook results in [source/hook.c](../../source/hook.c), and expression-block execution in [source/expr2.c](../../source/expr2.c).
