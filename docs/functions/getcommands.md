# getcommands function

## Synopsis

`$getcommands()`

`$getcommands(<pattern> [<pattern> ...])`

## Technical

Returns the names of registered built-in commands. With no input, all built-in command names are returned. Otherwise, each supplied pattern selects names using EPIC's case-insensitive wildcard matcher. This matches complete names, rather than doing an implicit prefix search; use `prefix*` for a prefix.

Patterns are ordinary whitespace-separated words. Double quotes do not group a pattern, even with `/xdebug dword` enabled. Multiple patterns are processed in their supplied order and their result lists are concatenated. Duplicates are not removed, so a command matching two patterns appears twice.

Each pattern's matches follow the global symbol table's sorted name order. Built-in names are returned in uppercase, without a leading slash. There is no output to the screen and no command execution.

Only symbols with a built-in command component are included. User-only aliases and alias stubs do not qualify; an alias sharing a built-in name does not hide that built-in from the list. The direct registry walk does not resolve stubs or load their scripts. Build-dependent commands appear according to what is registered in the running client.

## Practical

This is useful for completion scripts and command discovery. Use [$cexist()](cexist.md) to test one exact built-in name, [$getfunctions()](getfunctions.md) for built-in function names, or [$aliasctl()](aliasctl.md) to inspect user aliases.

## Returns

A space-separated list of names matching the supplied patterns, or the empty string when no names match. With no input, it returns the full registered built-in command list.

## Examples

| Expression | Result |
| --- | --- |
| `$getcommands(*echo*)` | `ECHO XECHO` |
| `$getcommands(echo)` | `ECHO` |
| `$getcommands(echo echo)` | `ECHO ECHO` |
| `$getcommands(x*)` | All registered built-in commands beginning with `x`. |

## Source

Registration and `GET_UNIFIED_ARRAY_FUNCTION`: [source/functions.c](../../source/functions.c). Built-in component enumeration and wildcard selection: [source/alias.c](../../source/alias.c). Sorted symbol storage: [source/alist.c](../../source/alist.c). Built-in command registration: [source/commands.c](../../source/commands.c). Wildcard matching: [source/reg.c](../../source/reg.c).
