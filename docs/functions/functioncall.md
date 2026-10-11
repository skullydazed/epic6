# functioncall function

## Synopsis

`$functioncall()`

## Technical

Returns `1` when the current script scope is a function call expecting a return value, such as an alias invoked as `$name(arguments)`. Returns `0` for an alias invoked as a command and for ordinary command callbacks such as [/on](../commands/on.md) bodies and [/userhost -cmd](../commands/userhost.md) bodies. Arguments supplied to `$functioncall()` are ignored.

This tests the current local scope, rather than whether any enclosing caller is a function. An alias called as a command from inside a function has its own command scope and reports `0`; normal control blocks within the same function scope continue to report `1`. Script bodies explicitly called as lambda functions also report `1`.

## Practical

Use this function to let an alias return a value when used as a function, but display it when used as a command. It does not invoke another function or test whether a function exists; [$fnexist()](fnexist.md) provides the latter test.

## Returns

`1` for the current function-call scope, otherwise `0`. The query does not change the function's return value.

## Examples

```irc
alias greeting {
    if (functioncall()) {return Hello}
    echo Hello
}
```

`greeting` displays `Hello`; `$greeting()` returns `Hello`. The function form uses [/return](../commands/return.md) to supply its value rather than displaying it.

## Source

Registration and current-frame comparison: [source/functions.c](../../source/functions.c). Named alias and lambda call setup, return-value scope, and restoration: [source/commands.c](../../source/commands.c). Local stack state: [source/alias.c](../../source/alias.c). Initial top-level scope: [source/irc.c](../../source/irc.c).
