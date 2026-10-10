# call command

## Synopsis

```text
/call
```

## Description

`call` displays a debugger-like stack trace of the script scopes currently executing. Use it inside an alias or callback to see the chain of active calls that led there.

The output begins with `Call stack`, lists frames from the current frame back toward frame 0, and ends with `End of call stack`. Each entry contains its stack index and the statement currently recorded for that frame. The current `call` statement appears first, at the highest active index; the indexes decrease toward the oldest frame. A frame without a recorded statement appears as `<async>`.

Statements are recorded before command-name normalization and `$` expansion. Their original spelling, arguments, and any leading command slash can therefore appear in the listing. This is a script-scope trace, not a native C backtrace, and it does not list local variable values or create a file.

There are no options or required arguments. Supplied arguments are ignored. The command is available in every build, is strictly local, and requires no IRC connection. It displays text rather than returning a list as a function result. Output uses EPIC's diagnostic `yell` mechanism, so a [`yell`](../on/yell.md) hook can handle or suppress it.

Some internal panic and trapped-error paths print a similar trace directly to standard error. They use `panic_dump_call_stack()` rather than executing the `call` command; a trace is not guaranteed for every kind of crash.

## Example

Define two aliases in a script:

```text
alias outer {
    inner
}
alias inner {
    call
}
```

When you run `/outer`, the trace starts with the `call` statement in `inner`, followed by the `inner` invocation in `outer`, then the older calling context. Exact indexes and additional frames depend on that context.

## History

The `call` command first appeared in EPIC3.001.

## Source

See [source/commands.c](../../source/commands.c) for the registration, `e_call()`, and statement recording in `parse_statement()`. [source/alias.c](../../source/alias.c) implements scope management, `dump_call_stack()`, and `panic_dump_call_stack()`. [source/output.c](../../source/output.c) implements `yell()`; [source/ircaux.c](../../source/ircaux.c) and [source/irc.c](../../source/irc.c) contain internal error paths that print panic traces.
