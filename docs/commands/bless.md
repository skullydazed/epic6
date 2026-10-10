# bless command

## Synopsis

```text
/bless
```

## Technical

Ordinarily, the local variables of an atomic scope, such as an alias or callback, cannot be accessed from another atomic scope. Sometimes two scopes need to cooperate so an inner call or callback can update the enclosing scope's local variables. The enclosing scope uses [`wait`](wait.md), and the inner scope uses `bless`.

`bless` changes the current local scope's parent to the oldest locked stack frame below it. Local-variable lookup can then continue into that waiting scope. A local variable already present in the inner scope still takes precedence; new variables that do not already exist in a visible scope remain local to the inner scope. Blessing does not copy variables or extend their lifetime.

If no enclosing frame is locked, no enclosing scope is attached. `bless` does not automatically expose the immediately calling alias's variables. It also does not consume or unlock a waiting frame: multiple blessed callbacks can attach to the same frame while it remains locked.

Nested or overlapping waits can select an older waiting scope than the one you intended. The command does not identify a waiting scope by name or request, so avoid relying on it to distinguish several simultaneous waits.

The built-in currently ignores all arguments, produces no output, and performs no argument validation. Supply no arguments. It is available in every build and does not itself require an IRC connection, send a request, wait for anything, or guarantee that an asynchronous callback will arrive. Those requirements belong to the surrounding commands.

## Examples

To share a variable between aliases:

```text
alias one {
    bless
    @ :hi = 'this is a test'
}
alias two {
    @ :hi = 'this is the first value'
    one
    echo $hi
    wait for { one }
    echo $hi
}
```

Calling `/two` displays `this is the first value` and then `this is a test`. The first call to `one` has no waiting parent to attach to, so its assignment stays in its own scope. Inside `wait for`, `bless` lets the assignment find and update `two`'s existing local `hi`.

The same technique can collect information from a [`userhost`](userhost.md) callback while the enclosing scope waits for server replies:

```text
alias uh {
    ^local blahblah
    wait for {
        ^userhost $* -cmd {
            bless
            push blahblah $3@$4
        }
    }
    return $blahblah
}
```

Called as a function, for example `$uh(joebob)`, this collects callback user and host fields into the local result variable. It requires a registered server and usable replies; it does not make remote server responses reliable. Keep other pending waits in mind because the oldest locked frame is selected.

## History

The `bless` command first appeared in EPIC4pre0.009.

## Source

See [source/commands.c](../../source/commands.c) for `blesscmd()`, `waitcmd()`, and alias/callback scope creation. [source/alias.c](../../source/alias.c) implements `bless_local_stack()`, oldest-frame selection, locking, and local-variable lookup. [source/server.c](../../source/server.c) implements stack locking during `server_hard_wait()`; [source/who.c](../../source/who.c) creates the `userhost` callback scope and its positional arguments.
