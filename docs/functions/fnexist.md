# fnexist function

## Synopsis

`$fnexist(<function>)`

## Technical

Tests whether a built-in function is registered under the supplied name. The first argument word is used; additional words are ignored. Names are case-insensitive and must match exactly, without a leading `$` or parentheses. This does not test whether a user alias exists.

Argument parsing honors [/xdebug dword](../commands/xdebug.md). Missing input returns the empty string. An explicitly empty name, supplied as a quoted word in that mode, returns `0`.

## Practical

Use this function to decide whether the running client provides a built-in before supplying a script replacement for older versions. Build-dependent functions are reported according to what is registered in this client.

A user alias with the same name neither hides nor satisfies this test: a built-in component yields `1`, while an alias-only name yields `0`. To inspect user aliases, use [$aliasctl()](aliasctl.md); to test built-in commands, use [$cexist()](cexist.md).

The lookup can resolve a [/stub](../commands/stub.md) attached to the same global symbol, loading its script before checking the built-in component. It does not call the requested function, but lazy loading can have script side effects. The test uses the current global symbol table rather than an immutable list of functions originally compiled into the binary.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | A built-in function component exists under the name. |
| `0` | No built-in function component exists under the name. |
| Empty string | No input was supplied. |

## Examples

| Expression | Result |
| --- | --- |
| `$fnexist(floor)` | `1` |
| `$fnexist(FLOOR)` | `1` |
| `$fnexist(floor extra)` | `1`; only the first word is tested. |
| `$fnexist(floor())` | `0`; parentheses are not part of a registered name. |
| `$fnexist()` | Empty string |

## Source

Registration, `function_fnexist()`, and `func_exist()`: [source/functions.c](../../source/functions.c). Built-in component lookup and stub resolution: [source/alias.c](../../source/alias.c). Argument parsing: [include/functions.h](../../include/functions.h) and [include/words.h](../../include/words.h).
