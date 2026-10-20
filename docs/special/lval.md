# Lvalues (lvals)

An **lvalue** identifies a variable that can be assigned to. It is the destination on the left of an assignment, but it is also used by operations that modify a named variable, such as incrementing a counter or removing a word from a list.

## Where lvalues are used

| Form | Effect |
| --- | --- |
| `/assign name text` or `@ name = [text]` | Assign text using the local/global selection rules below. |
| `/local name text` | Create or update a local variable. |
| `/stub assign name filename` | Arrange to load a variable definition on demand. |
| `@ name++`, `@ --name`, or compound assignment | Change the named variable through an expression operator. |
| `$push(name text)`, `$unshift(name text)` | Append or prepend text to a variable. |
| `$shift(name)`, `$pop(name)` | Remove and return the first or last word of a variable. |
| `$splice(name start count replacement)` | Replace a range of words in a variable, returning the removed words. |
| `$shiftbrace(name)` | Remove a leading braced expression from a variable. |
| `fe`/`fec` loop variables and [argument-list](arglist.md) parameters | Bind values to local names according to those constructs' rules. |

The `push`, `pop`, `shift`, and `unshift` commands also modify named variables. Other interfaces, including `aliasctl`, `getopt`, and replacement functions with variable-update modes, have their own lvalue arguments. Consult each interface for its argument syntax and return value; accepting a variable name does not imply that every operation declares a global variable.

## What an lvalue looks like

The ordinary name syntax uses letters, digits, underscores, and dots, with optional bracketed name components. Names are case-insensitive. An optional leading colon qualifier selects assignment storage:

| Prefix | Assignment destination |
| --- | --- |
| No colon | An already visible local variable, if present; otherwise a global variable. |
| `:name` | Local storage. |
| `::name` | Global storage, bypassing locals. |

`local` explicitly creates local storage. Scope lasts according to the surrounding [atomic scope](atomic_scope.md), not simply to the end of the nearest brace pair. The special `function_return` variable is managed as a function-local return slot.

Bracketed components construct ordinary dotted variable names, not entries in the separate [Karll array](karll_arrays.md) facility. For example, `record[key]` becomes `record.key`. Bracket contents use text expansion rather than implicit arithmetic evaluation: `record[1 + 2]` does not automatically mean `record.3`. Use an explicit expression expansion when calculation is intended, and ensure the resulting name contains only valid name characters.

The expression parser can also expand name pieces outside brackets, such as `myvar$testing`. The resulting name still goes through assignment validation: spaces, punctuation outside the permitted name syntax, or other unsuitable expanded text can cause an error. Bracketed name construction is often clearer.

## Examples

Inside an alias or script scope:

```irc
assign ::myvar one;
local myvar two;
echo $myvar;
echo $::myvar;
assign :othervar one;
assign ::othervar two;
assign othervar three;
```

The two `echo` statements print `two` and `one`. The final assignment updates local `othervar` to `three`; global `othervar` remains `two`.

For a dynamic component:

```irc
@ testing = [suffix];
@ myvar[$testing] = [one];
echo $myvar.suffix;
```

This stores and prints `one` under the name `myvar.suffix`. `@ myvar$testing = [one]` is also permitted by the current expression parser, but constructs `myvarsuffix`, without the dot inserted by brackets.

## Lvalues versus values

A variable reference can supply either a destination name or its current value depending on the operation. A string literal such as `[myvar]` is a value, not automatically an expression lvalue: `@ [myvar] = [one]` produces a non-lvalue diagnostic. Unary `*` can deliberately turn a computed value into a variable name; for example, `@ *[myvar] = [one]` assigns to `myvar`.

These assignment qualifiers must not be confused with a guarantee about reads: ordinary `$:name` lookup currently falls back to the global/built-in symbol table if no local value is found. Explicit `:name` assignment still selects local storage. See [expando substitution](expando_substitution.md) for read lookup.

Assignment to an empty value can remove a global variable, whereas a local can retain an empty string. Consequently, an undefined variable and an empty local can produce the same displayed text while differing in storage and later lookup behavior. Invalid destinations can report diagnostics without changing the requested variable; expression evaluation does not roll back earlier side effects after an error.

## Source references

- `source/alias.c`: `add_var_alias()`, `add_local_alias()`, stub assignment, and scope selection.
- `source/expr2.c`: lvalue tokenization, `after_expando_special()`, `get_token_lval()`, indirection, and assignment operators.
- `source/ircaux.c`: `after_expando()` name recognition and `remove_brackets()` name normalization.
- `source/functions.c`: variable-modifying functions including push/pop/shift/splice.
- `source/commands.c` and `source/if.c`: command forms, aliases, argument-list blocks, and loop variables.
