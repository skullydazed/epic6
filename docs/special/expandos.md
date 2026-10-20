# Expandos

## What is an expando?

An expando is a `$` form that acts as a placeholder for other text in an EPIC statement. When a string is expanded, EPIC replaces each expando with its value. Values can come from variables, client state, arguments, functions, or expressions.

Expansion happens in script statements and other evaluation contexts; ordinary interactive input does not automatically receive the same expansion pass. See [expando substitution](expando_substitution.md) for expansion timing, protection, modifiers, and the complete syntax.

## What kinds of expandos are there?

| Syntax | Explanation |
| --- | --- |
| `$variable` | Look for a visible local variable, then a global variable, built-in expando, or built-in setting; if none supplies a value, try the process environment. An unresolved name produces an empty string. |
| `$:variable` | Request local lookup. In the current implementation, a missing local value still falls back to the global/built-in symbol table; this is not a strict local-only lookup. |
| `$::variable` | Skip local variables and use the global/built-in lookup. |
| `$x` | A single-letter built-in expando, when no user variable shadows that name. For example, `$n` gives the current window's nickname and `$x` gives the userhost for the current server context. |
| `$function(arguments)` | Expand the arguments and substitute the function's return value. Built-in functions and script-defined function aliases are supported. |
| `$0`, `$1`, `$*` | Select arguments supplied to the current evaluation. Numbering starts at zero; `$*` is the entire argument string. Ranges and a last-argument form are also available. |
| `${expression}` | Evaluate an expression and substitute its result. |
| `$(text)` | Expand the text and use the result as an expando name: an indirect lookup. |
| `$$` | Insert a literal dollar sign. |

EPIC variable, function, and built-in expando names are case-insensitive. Environment lookup uses the original spelling of the requested name, including any leading colons; the operating system's environment names may be case-sensitive. For visibility rules, see [atomic scope](atomic_scope.md). Variables can shadow built-in expandos and settings, so an assignment to `n`, for example, changes what ordinary `$n` returns.

## Common built-in expandos

| Expando | Value |
| --- | --- |
| `$n` | Nickname on the current window's server, or empty without a server. |
| `$c` | Current window's channel, or `0` when there is none. |
| `$q` | Current window's query target, or empty. |
| `$t` | Current window's send target: query first, otherwise channel, or empty. |
| `$s` | Server name for the server being parsed, falling back to the current window's server. |
| `$a` | Away message for the current server context. |
| `$l` | Current input-line text. |
| `$u` | The client's cut buffer. |
| `$w` | Process working directory, or empty if it cannot be read. |
| `$z` | The client's formatted clock string. |

These are reads of local client state, not requests to an IRC server. Server-related values depend on the active evaluation context and on information already received from the server. Window-based values and server-context values need not describe the same connection inside every hook or callback. Script-provided variables and functions can add further expandos after their scripts are loaded.

## Example

```irc
alias whereami { echo Nick: $n Channel: $c Target: $t }
```

After defining this alias, `/whereami` expands the values when the alias runs. If there is no channel, the channel field contains `0`; an unset target is empty.

## Source references

- `source/functions.c`: `built_in[]`, built-in expando handlers, and `call_function()`.
- `source/alias.c`: `get_variable_with_args()` and symbol lookup.
- `source/expr.c`: expansion and special `$` forms.
- `source/ircaux.c`: `after_expando()` recognizes expando names and function calls.
- `source/window.c`: window channel, query, and send-target selection.
