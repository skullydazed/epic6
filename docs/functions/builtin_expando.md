# builtin_expando function

## Synopsis

```text
$builtin_expando(expando-name)
```

## Technical

`builtin_expando()` directly invokes the built-in inline expando registered under `expando-name`, returning its current value. Missing input or a name without a built-in expando returns the empty string. It does not invoke an ordinary built-in function such as `abs()`, nor does it return a setting's value.

Supply the registered expando name alone, without a dollar sign, parentheses, quotes, or additional arguments. The entire argument string is used for exact symbol lookup; it is not split into words or converted to uppercase. Standard letter expandos are registered with uppercase names, so use `N`, for example, rather than `n`. The function name itself remains case-insensitive.

## Practical

Inline expandos such as `$A`, `$B`, and `$C` can be shadowed by user variables. This function ignores the user variable value and selects the underlying built-in expando component instead. For example, assigning a value to `N` does not change what `$builtin_expando(N)` returns.

The selected expando runs in the existing client context; this function does not select another window or server or create a fresh alias scope. Its value and effects are those of that particular expando. Global symbol lookup can resolve a [`stub`](../commands/stub.md) on the same symbol, loading its script before dispatch, so the call is not guaranteed to be free of script side effects. An expando removed from the symbol table cannot be recovered through this function.

## Examples

| Expression | Return value |
| --- | --- |
| `$builtin_expando(N)` | Nickname for the current window's server, or empty if that window has no server |
| `$builtin_expando(C)` | Current window's current channel, or `0` if none |
| `$builtin_expando(abs)` | Empty string: this is not an inline expando |
| `$builtin_expando()` | Empty string |

## Returns

The value supplied by the selected inline expando, even when a user variable shadows it. An empty result can mean a missing expando or a legitimate empty value from an existing one.

## Source

[source/functions.c](../../source/functions.c) registers `builtin_expando`, lists the standard inline expandos, and implements `function_builtin()`, `alias_nick()`, and `alias_channel()`. [source/alias.c](../../source/alias.c) implements `get_var_alias()` and global symbol/stub lookup.
