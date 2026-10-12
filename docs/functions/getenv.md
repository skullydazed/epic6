# getenv function

## Synopsis

`$getenv(<variable>)`

## Technical

Returns the value of a variable in EPIC's process environment. This is the environment inherited when the client started, as modified within the client; it is not a live query of the parent shell.

Only the first argument word is used, and additional words are ignored. Normal function parsing honors [/xdebug dword](../commands/xdebug.md) for double-quoted words. Environment variable names are case-sensitive on EPIC's Unix platforms, even though function names are case-insensitive. Supply the name without a leading `$`.

The lookup is independent of [/set](../commands/set.md), [/assign](../commands/assign.md), and local variables. A script variable with the same name does not shadow this explicit environment lookup. The returned value is copied as text, without expansion or word splitting.

## Practical

Use this when a `/set` or `/assign` variable conflicts with an environment variable. [/setenv](../commands/setenv.md) can change or remove environment variables within EPIC; subsequent `$getenv()` calls reflect those changes. Later changes in the shell that launched EPIC do not automatically update EPIC's environment.

## Returns

The environment variable's value, or the empty string if the variable is unset, its value is empty, or required input is missing. This function does not distinguish an unset variable from an empty value. It does not print the value or change the environment.

## Examples

| Expression | Meaning |
| --- | --- |
| `$getenv(HOME)` | The process environment's `HOME` value, if set. |
| `$getenv(PATH ignored)` | The `PATH` value; the extra word is ignored. |
| `$getenv()` | Empty string. |

For a harmless demonstration variable:

```irc
setenv EPIC_DEMO hello
@ example_value = getenv(EPIC_DEMO)
setenv -EPIC_DEMO
```

`example_value` receives `hello`; the final command removes the demonstration environment variable.

## Source

Registration and the direct C `getenv()` lookup: [source/functions.c](../../source/functions.c). Environment modification: [source/commands.c](../../source/commands.c). Argument parsing and return copying: [include/functions.h](../../include/functions.h) and [include/words.h](../../include/words.h).
