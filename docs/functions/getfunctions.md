# getfunctions function

## Synopsis

`$getfunctions()`

`$getfunctions(<pattern> [<pattern> ...])`

## Description

Returns a word list of registered built-in function names. User-defined alias functions are not included. With no input, all built-in function names are returned; otherwise, each supplied pattern selects names using EPIC's case-insensitive wildcard matcher. This matches complete names, so use `prefix*` for a prefix search.

Patterns are ordinary whitespace-separated words. Double quotes do not group a pattern, even with `/xdebug dword` enabled. Multiple patterns are processed in their supplied order and their result lists are concatenated. A function matching more than one pattern appears more than once; use [$uniq()](uniq.md) if duplicates are unwanted.

Each pattern's matches follow the global symbol table's sorted name order. Names are returned in uppercase, without a leading `$` or parentheses. The function does not call or display the listed functions.

Only symbols with a built-in function component qualify. A user alias sharing a built-in name does not hide that built-in from the result, while an alias-only name is excluded. The direct registry walk does not resolve stubs or load scripts. Build-dependent functions appear according to what is registered in the running client.

## Returns

A space-separated list of built-in names matching the patterns, or the empty string if nothing matches. No input returns the full registered list.

See [$fnexist()](fnexist.md) for one exact built-in function test, [$getcommands()](getcommands.md) for built-in commands, and [$aliasctl()](aliasctl.md) for user aliases.

## Examples

| Expression | Result |
| --- | --- |
| `$getfunctions(floor)` | `FLOOR` |
| `$getfunctions(FLOOR)` | `FLOOR` |
| `$getfunctions(floor floor)` | `FLOOR FLOOR` |
| `$getfunctions(get*)` | Registered built-in functions beginning with `get`. |

## History

First appeared in EPIC4-1.1.3.

## Source

Registration and `GET_UNIFIED_ARRAY_FUNCTION`: [source/functions.c](../../source/functions.c). Built-in component enumeration: [source/alias.c](../../source/alias.c). Sorted symbol storage: [source/alist.c](../../source/alist.c). Wildcard matching: [source/reg.c](../../source/reg.c).
