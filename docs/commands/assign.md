# assign command

## Synopsis

```text
/assign
/assign <name-or-prefix>
/assign -<name>
/assign <name> <value>
```

## Description

`assign` is the general-purpose command for creating and changing variables, particularly text values. EPIC stores user variables as strings. A value used in a numeric expression is converted to a number as needed; assigning it does not give the variable a permanent numeric type.

With no arguments, `assign` lists global user variables. With a name or prefix and no value, it lists matching global variables, case-insensitively. Dotted descendants may appear grouped as `<Structure>` entries; use a more specific prefix to see individual values. These forms do not list local variables or built-in settings.

With a name and nonempty value, it creates or replaces the variable and normally prints a confirmation. Whitespace before the value is skipped; the remaining text is stored as supplied to the handler, without removing quotation marks. Normal `$` expansion may already have occurred before the handler runs, depending on the calling context.

With only `-name`, it removes that exact global user variable. It does not remove every variable sharing a prefix, a same-name command alias, or a local variable. `/assign -` reports that a name is required. If no matching symbol exists, the command reports `No such assign`.

The command has no options, is available in every build, and needs no server connection.

## Names and scope

Names are case-insensitive; values preserve their text and case. The normal name characters accepted by the implementation are letters, digits, underscores, and dots. A leading letter is not required, but names beginning with a letter or underscore are easier to reference because numbered expandos such as `$0` have another meaning. Invalid characters produce an error rather than an assignment.

Square brackets are converted to dot notation: `foo[bar][baz]` becomes `foo.bar.baz`.

An ordinary assignment updates a visible local variable if one exists; otherwise it creates or updates a global variable. Prefix the name with `:` to force local assignment or `::` to force global assignment:

```text
assign :name local value
assign ::name global value
```

`function_return` is special: assignment routes it to the local return-value machinery even with the global prefix. See [`alias`](alias.md) for function return behavior. The `-name` deletion form goes directly to global deletion and does not implement these scope prefixes.

To create local variables explicitly, see [`local`](local.md). Listing with `assign` always examines global variables, even when an ordinary assignment would update a local variable.

## Expressions and empty values

`assign` is primarily useful for string assignments. Use `${...}` where command expansion is active to insert the result of an expression. For arithmetic or an explicitly empty value, use an expression statement instead:

```text
@ foo = 4 + 5
@ foo = []
```

`/assign foo` lists variables rather than setting `foo` to an empty string. Quoting an empty value as `""` passes quotation characters to this handler; it is not an empty-value syntax. See [expressions](../expressions.md).

## Examples

To assign a text string to `$foo`:

```text
assign foo this is some text string
```

To compute the sum of two integers in a script context that expands `${...}`:

```text
assign foo ${4 + 5}
```

To list matching global variables and then delete `foo`:

```text
assign foo
assign -foo
```

At the interactive input prompt, prefix command forms with `/`.

## See also

[Expressions](../expressions.md), [special variables](../special_vars.md), [`alias`](alias.md), [`eval`](eval.md), and [`local`](local.md).

## History

The `assign` command first appeared in ircII.

## Source

See [source/alias.c](../../source/alias.c): `assigncmd()`, `add_var_alias()`, `add_local_alias()`, `find_local_alias()`, `list_var_alias()`, and `delete_var_alias()`. [source/ircaux.c](../../source/ircaux.c) implements name canonicalization and validation in `remove_brackets()` and `after_expando()`. [source/commands.c](../../source/commands.c) implements expansion before command dispatch.
