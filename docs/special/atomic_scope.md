# Atomic scopes

## What is an atomic scope?

An **atomic scope** defines the visibility and lifetime of local script variables. A new named scope begins with a fresh local-variable frame; [argument-list](arglist.md) parameters and a function's return variable can then be created before its body executes. When the scope ends, its local variables are destroyed.

The client creates a top-level scope during startup, so local variables can also be created from the input prompt. Those top-level locals can survive between input commands, but they are not the same storage as global variables and are normally hidden inside a new alias or callback scope.

Scopes are kept on a stack. Calling an alias or running a callback ordinarily hides the caller's ordinary locals rather than giving the callee access to them. Returning destroys the callee's frame and makes the caller's locals visible again. Reusing the same local name in two scopes does not by itself share a value.

“Atomic” here does not mean a transaction, a thread lock, or an uninterrupted period of execution. Waiting operations can process network input, timers, and other events while an outer scope remains suspended. Their handlers normally get their own scopes.

## Creating and using locals

Use [local](../commands/local.md) or an explicitly local assignment to create storage in the current scope:

```text
alias example {
    local message hello;
    @ :count = 1;
    echo $message $count;
};
```

The variables disappear when `example` returns. A bare assignment is not a local declaration: it updates an already visible local if one exists, otherwise it can create or update a global. The `::name` assignment form explicitly selects global storage. Globals are looked up separately when a name is not resolved locally.

Braces around an `if`, loop body, or ordinary command block do not by themselves create a new atomic scope. Calling an alias from that block does. Similarly, parsing a block with arguments is not enough to imply a new frame; the caller's execution interface determines scope creation.

## Where new scopes are created

The following built-in execution paths run named alias/callback frames:

- Each user alias invocation, whether used as a command or function.
- [on](../commands/on.md) callback bodies, including conditional hooks evaluated as functions.
- Files executed through the [pf loader](pf_loader.md).
- Commands executed by [defer](../commands/defer.md).
- Nonempty [exec](../commands/exec.md) output/error callbacks and termination or process-wait callbacks.
- User-created key-binding callback functions created through `bindctl`.
- Commands run from [queue](../commands/queue.md) or [timer](../commands/timer.md).
- Script-provided [set](../commands/set.md) callback bodies.
- [who](../commands/who.md) line/end, [ison](../commands/ison.md) on/off/end, and [userhost](../commands/userhost.md) command callbacks.
- Commands executed by server passive waits such as `wait -cmd`.

This describes these callback paths, not every command with a similar name. A built-in key action, a directly evaluated block, or another loader/callback interface need not create the same boundary. Input expansion and callback argument handling are separate from local-variable scope.

## Sharing local variables

Ordinary nested alias calls do not inherit caller locals. Pass needed values as arguments and return results, or deliberately use global storage when its lifetime and shared visibility are appropriate.

For more specialized callback cooperation, [wait](../commands/wait.md) can lock the current frame while processing events or commands. [bless](../commands/bless.md) changes the current frame's parent link so it can resolve locals from a locked frame. It does not copy those variables, extend their lifetime beyond the enclosing call, or make all previous frames visible.

In the current implementation, `bless` selects the first locked frame found while scanning from the oldest frame upward. Nested locks therefore do not imply that it selects the nearest caller. With no locked frame, it has no enclosing frame to expose. Use this mechanism only when the enclosing lifetime and callback path are understood; a later timer is not automatically a closure over the alias that created it.

`function_return` also has special lookup rules tied to the active function-call level. Treat it as the function-result mechanism rather than an ordinary name proving that a command callback can see all its caller's locals.

## Ending a scope and limits

Normal completion or a handled `return` releases the named call frame. Values needed afterward must have been returned or stored elsewhere; references to a destroyed local cannot recover it. Loading or invoking more code does not turn these locals into persistent configuration.

The implementation limits runtime stack frames to 20000. Excessive nesting reports that the handler could not run because of too much recursion. This boundary is not a substitute for terminating recursive scripts correctly. Scope operations require no optional platform feature or server request and have no independent command-return contract.

## History

Local variables first appeared in EPIC4pre0.009.

## Source references

- [source/alias.c](../../source/alias.c): `make_local_stack`, `destroy_local_stack`, `find_local_alias`, explicit local/global assignment selection, locking, and `bless_local_stack`.
- [source/commands.c](../../source/commands.c): `parse_line_alias_special`, alias/lambda invocation, pf loading, deferred commands, and waits.
- [source/irc.c](../../source/irc.c): creates the `TOP` local frame.
- [source/hook.c](../../source/hook.c), [source/exec.c](../../source/exec.c), [source/keys.c](../../source/keys.c), [source/queue.c](../../source/queue.c), and [source/timer.c](../../source/timer.c): named callback execution paths.
- [source/vars.c](../../source/vars.c), [source/who.c](../../source/who.c), and [source/server.c](../../source/server.c): setting, query, and passive-wait callbacks.
