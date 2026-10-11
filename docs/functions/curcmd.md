# $curcmd()

## Synopsis

`$curcmd()`

## Technical

Returns the name of the recognized built-in command or user-created command alias currently being dispatched. The name is uppercase, without a leading slash or arguments. All arguments supplied to this function are ignored.

Expression statements and block statements do not change this value. A nested recognized command temporarily replaces it, and completing that command restores the enclosing value. This is an active execution context, not a persistent record of the last command typed.

Command text, including function calls in its arguments, is expanded **before** the new command becomes current. Therefore, `$curcmd()` while expanding `echo $curcmd()` inside an alias reports the enclosing alias, not `ECHO`. Calling an alias as a function does not itself install its function name as the current command.

Unknown commands do not become current commands. Their processing retains the enclosing value, if any, including during an unknown-command hook.

## Returns

Returns the uppercase command name for the current recognized dispatch. Returns an empty string when there is no such active context.

## Examples

```irc
alias report_command {
    @ result = curcmd()
    echo Current command: $result
}
```

Invoking `/report_command` makes the expression assign `REPORT_COMMAND` to `result`, and the following `echo` displays that value. The expression statement does not replace the alias's command context.

## History

This function first appeared in EPIC5-0.3.2.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). [source/commands.c](../../source/commands.c) sets and restores the command context around recognized dispatch and performs command expansion beforehand.
