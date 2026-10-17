# on unload

## Synopsis

```text
/on [<noise>]unload "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered whenever the built-in [unload](../commands/unload.md) command is given a package name to unload. It lets a packaged script announce its exit or perform cleanup before EPIC removes its definitions.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the package name. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` / `$*` | Package name supplied as the first argument to `unload`. |

The hook receives the argument to `unload`, not a fresh copy of the arguments originally passed to [package](../commands/package.md). Additional `unload` arguments are ignored. Use a single-word package name: although `package` stores its whole argument string during a load, `unload` takes only its first word.

## Ordering and effects

The hook runs before EPIC removes the package's aliases, global variables, hooks, key bindings, and timers, in that order. A hook belonging to the package can therefore run before it is itself removed, and its package's aliases and variables are still available at entry.

Its suppression result is ignored. It cannot cancel unloading or suppress the command's progress messages, which announce each removal stage and finish with `Done.`. Use the handler for cleanup that the command does not provide automatically; unloading definitions does not undo arbitrary earlier script effects.

No check for an existing package precedes this event. An unknown package name still triggers it and the removal pass. An `unload` command without a name reports `You must supply a filename for /UNLOAD.` and does not invoke the hook. Package matching for alias, variable, and hook removal is case-sensitive, so use the package's declared spelling.

The event does not send an IRC request or notify a server by itself.

## Example

In a script declaring `package superscript`, announce its exit:

```text
on ^unload "superscript" {
    xecho -b SuperScript exiting. To infinity, and lots farther!;
};
```

Running `/unload superscript` invokes the handler before removing the package's definitions. The `^` noise mode does not cancel that removal.

## Source references

- [source/alias.c](../../source/alias.c): `unloadcmd` invokes the hook before removal; `unload_cmd_alias` and `unload_var_alias` match package ownership.
- [source/commands.c](../../source/commands.c): `packagecmd` records package text in the current load context.
- [source/hook.c](../../source/hook.c): event registration, matching, and `unload_on_hooks`.
- [source/keys.c](../../source/keys.c) and [source/timer.c](../../source/timer.c): package key binding and timer removal.
