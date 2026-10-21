# Ignore

The former built-in `/ignore` command and its ignore-rule subsystem are retired in EPIC6. For the proposed replacement, see the [ignore script](../scripts/ignore.md), loaded explicitly with `/load -pf ignore`.

The script is unfinished. It does not reliably restore the former sender/channel masks, level selection, longest-pattern precedence, highlight/exception rules, list display, exact-pattern removal, or automatic timeout expiry. In particular, its list-display entry point is missing, several helpers are empty, and its suppression hook reads a different pattern field from the one created by the command.

Thus familiar forms such as `/ignore foobar all`, `all -publics`, `^notices`, `reason`, `timeout`, or `none` cannot be assumed to implement their historical effects. The script page describes its actual behavior and defects; loading the file alone is not a dependable ignore guarantee.

For constructing explicit client-output policies, see [on](../commands/on.md), [context](../on/context.md), [shh](../commands/shh.md), and [levels](../special/levels.md). Suppressing local output does not stop the server sending data, undo client state changes, or erase earlier history.

## Source references

- `KNOWNBUGS`: retirement of the former ignore subsystem.
- `source/commands.c` and `source/functions.c`: current registries lack the former built-in interfaces.
- `script/ignore`: incomplete replacement alias, data structure, and context hook.
- `source/window.c` and `source/screen.c`: context suppression flag and display checks.
