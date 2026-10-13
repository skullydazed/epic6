# $startupfile()

## Synopsis

`$startupfile()`

## Return value

Returns the filename or script name selected for the client's startup load. It is recorded before [/load](../commands/load.md) runs, so a nonempty result does not prove the file loaded successfully. If startup loading was skipped with `-q`, or has not reached the selection step, the result is empty.

All arguments are ignored by the C handler. The function reads the stored selection without loading a file, checking its current existence, or changing startup settings.

## Technical

Startup path selection uses this precedence:

1. `-l <file>` or `-L <file>` overrides the environment selection.
2. `EPICRC` selects a pathname when present; otherwise `IRCRC` does.
3. Without either environment variable, EPIC selects `~/.epicrc` if it exists, otherwise `~/.ircrc`.

At the startup-load step, a selected pathname that passes the operating-system readability check is stored and passed to `/load`. Otherwise the stored selection is the script name `global`, which `/load` resolves through its normal search rules. Override strings are retained as supplied, rather than reported as a canonical absolute pathname. The initial readability check does not perform tilde expansion on an override.

The selected value is not changed by subsequent manual loads or dumps. This is not a query for the file currently being loaded; [$loadinfo()](loadinfo.md) reports loader context.

## Practical

The stored selection can help reload startup definitions after [/dump](../commands/dump.md). Reloading uses current `/load` resolution and settings, so it need not reproduce the original load. `/dump all` also does not recreate a fresh client process or reset all client state.

## Examples

```irc
dump all
load $startupfile()
```

This discards the categories affected by `/dump all` and attempts to reload the stored startup selection. It assumes the result is nonempty and suitable for `/load`'s filename parsing. `/load` does not group filenames with double quotes, so paths containing spaces require particular care rather than adding quotes blindly.

With `-q`, `$startupfile()` returns empty. If no readable selected startup pathname exists, it returns `global` even if loading that script subsequently fails.

## History

First appeared in EPIC5-0.0.2.

Registration and `function_startupfile()`: [source/functions.c](../../source/functions.c). Startup options, environment selection, and `load_ircrc()`: [source/irc.c](../../source/irc.c). Default names: [include/irc.h](../../include/irc.h).
