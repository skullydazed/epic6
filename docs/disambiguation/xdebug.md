# Xdebug

Do you mean:

- The [xdebug command](../commands/xdebug.md), `/xdebug [+|-]option ... [{ commands }]`, which changes diagnostic and compatibility flags? With no arguments it lists accepted names, not their current states.
- The [xdebug function](../functions/xdebug.md), `$xdebug([patterns...])`, which queries flag states without changing them? It returns names prefixed with `+` or `-` according to enabled state.

Flags share a global mask. Some change parsing or runtime behavior rather than merely adding diagnostics, so consult the command page for the particular option. The command's trailing block provides temporary changes and restores the original complete mask afterward.

The function's default result includes an `ALL` entry whose enabled state means any bit is set. Replaying the unfiltered result is therefore not a faithful way to restore a partially enabled mask. See the function page for that limitation and pattern matching. These interfaces control the local client, not IRC server debugging.

## Source references

- `source/debug.c`: shared option table, mask updates, temporary block handling, and state query.
- `source/commands.c` and `source/functions.c`: interface registrations.
