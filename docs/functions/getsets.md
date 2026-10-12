# getsets function

## Synopsis

`$getsets()`

`$getsets(<pattern> [<pattern> ...])`

## Technical

With no input, returns a word list of all registered [set](../commands/set.md) settings. Otherwise, returns the names matching each supplied pattern using EPIC's case-insensitive wildcard matcher. Patterns match complete names; use `prefix*` for a prefix search.

Multiple patterns are ordinary whitespace-separated words. Double quotes do not group a pattern, even with `/xdebug dword` enabled. Patterns are processed in the order supplied, and their result lists are concatenated. Duplicates are not removed; use [$uniq()](uniq.md) if needed.

Each pattern's matches follow the global symbol table's sorted name order. Setting names are returned in uppercase. Results include built-in settings and typed settings registered by scripts through [$symbolctl()](symbolctl.md). An ordinary `/assign` alone is excluded; an assignment sharing a registered setting's name does not hide that setting.

This function lists names rather than values. Use [$getset()](getset.md) to retrieve a setting's value. Listing does not invoke setting callbacks, resolve stubs, or load scripts. The available names depend on the running client's build and currently registered script settings.

## Returns

A space-separated list of setting names matching the patterns, or the empty string if nothing matches. No input returns the full registered list. There is no diagnostic for an unmatched pattern.

## Examples

| Expression | Result |
| --- | --- |
| `$getsets(beep)` | `BEEP` |
| `$getsets(BEEP)` | `BEEP` |
| `$getsets(beep beep)` | `BEEP BEEP` |
| `$getsets(*display*)` | Registered setting names containing `display`. |

## History

First appeared in EPIC4pre1.046.

## Source

Registration and `GET_UNIFIED_ARRAY_FUNCTION`: [source/functions.c](../../source/functions.c). Setting component enumeration and script registration: [source/alias.c](../../source/alias.c). Built-in settings: [source/vars.c](../../source/vars.c). Sorted symbol storage: [source/alist.c](../../source/alist.c). Wildcard matching: [source/reg.c](../../source/reg.c).
