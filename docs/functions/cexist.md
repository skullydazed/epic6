# cexist function

## Synopsis

```text
$cexist(command)
```

## Technical

`cexist()` tests whether a built-in command is registered under the supplied name. The first argument word is used; additional words are ignored. Command names are case-insensitive and must match exactly, without the leading slash. This is not a test for a user alias or for a built-in function.

Argument word parsing honors [`xdebug dword`](../commands/xdebug.md). With no argument, the function returns the empty string. An explicitly empty name, when passed as a quoted word in that mode, returns `0`.

## Practical

Use this function to decide whether the running client provides a command before supplying a script replacement for it. A command available only in particular builds, such as one requiring Python support, is reported according to what is registered in this client.

A user alias with the same name neither hides nor satisfies this test: a built-in still yields `1`, while an alias-only name yields `0`. To inspect user alias bodies, use [`aliasctl()`](aliasctl.md); to test built-in functions, use [`fnexist()`](fnexist.md).

The lookup can resolve a [`stub`](../commands/stub.md) attached to the same global symbol, loading its script before testing the built-in component. The function does not execute the requested command, but such lazy loading can have script side effects. It tests the current symbol table, rather than an immutable list of commands originally compiled into the binary.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | A built-in command component exists under the name |
| `0` | No built-in command component exists under the name |
| Empty string | No argument was supplied |

## Examples

```text
$cexist(echo)      returns 1
$cexist(ECHO)      returns 1
$cexist(/echo)     returns 0
$cexist()         returns an empty string
```

## Source

[source/functions.c](../../source/functions.c) registers `cexist` and implements `function_cexist()`. [source/commands.c](../../source/commands.c) implements `command_exist()`, and [source/alias.c](../../source/alias.c) implements command-component and stub lookup. [include/functions.h](../../include/functions.h) and [include/words.h](../../include/words.h) define argument parsing.
