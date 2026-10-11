# $epic()

## Synopsis

`$epic()`

## Technical

Returns `1` to identify the client as EPIC. The current built-in is an unconditional constant: it does not inspect the client version, build options, server connection, or arguments. Arguments, if supplied, are ignored.

## Practical

Scripts can use this as an EPIC-family identification check. It does not establish that a particular command, function, or optional feature is available. [`$fnexist()`](fnexist.md) and [`$cexist()`](cexist.md) check registered built-in functions and commands.

The `$J` expando returns the client version string, while `$V` returns its internal numeric version identifier. These provide more specific version information than `$epic()`. The original distinction from other ircII clients is useful when their version identifiers overlap, but this implementation does not run a test that could identify another client at runtime.

## Returns

Always returns the decimal string `1`. The current C built-in has no path returning `0` or an empty string.

## Examples

```irc
if (epic()) {
    echo EPIC version: $J
}
```

The condition is true with this built-in implementation.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c), which also implements `$J` and `$V`. The underlying version identifiers are defined in [source/irc.c](../../source/irc.c).
