# foreach

Iterate through the immediate children of a variable or alias structure.

## Synopsis

```text
/foreach structure variable { commands }
/foreach -structure variable { commands }
```

## Description

`foreach` visits names in a structure, rather than words in a variable's value. By default it examines user-assigned variables in EPIC's global symbol table. Attach a hyphen to the structure name to examine user-defined command aliases instead. It does not enumerate local variables, built-in commands, or built-in settings.

For each immediate child, EPIC assigns the child's name relative to the structure to `variable` and executes the body. Names are normalized to uppercase. If `blah.one.value` exists, iterating `blah` produces `ONE`, not `ONE.VALUE`; use a nested loop to visit the next level. The root's own value is not an item. A nonexistent structure or one with no qualifying descendants executes no iterations and prints nothing.

The list of child names is copied before iteration. Removing entries inside the body is therefore supported; new entries added during the body are not included in that invocation. Child names are visited in the symbol table's sorted order, not assignment order. Values themselves are looked up when your body requests them.

The loop variable is [local](local.md). An existing visible local is updated, or a local is created in the current scope. The loop does not introduce a separate scope or restore an earlier value. The body receives the enclosing argument context. [continue](continue.md) moves to the next child; [break](break.md) exits the loop. Return and other propagated control-flow exceptions also stop iteration.

The structure name supports bracket notation, whose contents are expanded using the enclosing arguments. Body expansions normally occur when the body executes. Escaping the opening brace as `\{` allows the surrounding script expansion to expand body variables earlier; use this deliberately, because loop-variable values are normally needed at iteration time.

## Examples

Visit a structure two levels deep:

```text
foreach blah xx {
    foreach blah.${xx} yy {
        echo $blah[$xx][$yy]
    }
}
```

Remove the direct aliases in the `booya` structure:

```text
foreach -booya xx {
    alias -booya[$xx]
}
```

This example deletes direct child aliases. If a child is itself a deeper structure, traverse its descendants explicitly to remove those aliases too.

## Restrictions

Supply a structure name, one valid loop-variable name, and a brace-delimited body. An empty body is allowed. Missing arguments or malformed braces produce diagnostics. There are no options other than the attached hyphen selecting aliases, and the built-in is always registered.

Reference nested values as `$var.${subvar}.${subvar}` or `$var[$subvar][$subvar]`. Do not use `$var.$subvar.$subvar`: the expando parser can interpret the latter as a different nested variable expression.

Use [fe](fe.md) or [for](for.md) for iteration through a word list.

## Source

Registration is in [source/commands.c](../../source/commands.c), and iteration and control flow are in [source/if.c](../../source/if.c). Child-name selection and local assignment are in [source/alias.c](../../source/alias.c), with symbol lookup in [source/alist.c](../../source/alist.c). Bracket normalization is in [source/ircaux.c](../../source/ircaux.c), and deferred and escaped-brace expansion is in [source/expr.c](../../source/expr.c).
