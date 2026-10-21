# Shift

Do you mean:

- The [shift command](../commands/shift.md), `/shift variable-name`, which removes the first word of a variable and discards it?
- The [shift function](../functions/shift.md), `$shift(variable-name)`, which performs the same writeback and returns the removed word?

Supply the variable's name, not its expanded contents. These interfaces operate on a named client variable; `/shift` without an argument does not implicitly consume the first positional argument from `$*`. Quoted-word grouping depends on `xdebug dword`. See the individual pages for scope, empty values, and return behavior.

For adding at the front, see [unshift](unshift.md). For removing from the back, see [pop](pop.md). For retrieving without modifying a variable, see [word](../functions/word.md). None of these word-list operations sends an IRC request.

## Source references

- `source/commands.c`: command wrapper discards the function result.
- `source/functions.c`: `function_shift` lookup, extraction, writeback, and registration.
