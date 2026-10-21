# Local

For local variable declarations, see the [local command](../commands/local.md):

```text
/local name[,name...] [value]
/local structure.
/local -dump
```

`local blah this is a test` assigns that text to a visible local variable, creating it if necessary. A comma-separated list assigns the same value to each name. A declaration without a value creates or resets the local to empty. Local variables normally last until the enclosing [atomic scope](../special/atomic_scope.md) ends.

`local structure.` creates a marker for local structure members; it does not copy a global structure. `local -dump` clears the current frame's locals. The old `local -name` spelling is not an individual-variable deletion operation in the current command; consult the command page for details and limitations.

For programmatic access to local values, see the `local` domain of [aliasctl](../functions/aliasctl.md). For ordinary variable lookup, scope qualifiers, and global fallback, see [expandos](../special/expandos.md). These interfaces manipulate client variables, not IRC server state.

## Source references

- `source/alias.c`: local command, local-frame lookup, structure markers, and `aliasctl` domains.
- `source/commands.c`: `local` command registration.
