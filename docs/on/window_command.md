# on window_command

## Synopsis

```text
/on [<noise>]window_command "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered after each recognized subcommand executed by the built-in [window](../commands/window.md) command. It reports changes to that command's operative window: the window on which subsequent subcommands in the same statement operate. This need not be the window with input focus.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the old refnum, resulting refnum, and subcommand token together. There are no event-specific options.

Changing window state from this hook can invalidate the surrounding command's assumptions. Prefer queries and bookkeeping here; use [defer](../commands/defer.md) for window changes after the command finishes. In particular, do not destroy a window that remaining subcommands still expect to use.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | User-visible operative window refnum before the subcommand, or `-1` if unavailable. |
| `$1` | Resulting user-visible operative window refnum, or `-1` when the subcommand returns no usable operative window. |
| `$2` | Subcommand token as supplied, with a leading option-query `-` removed. |

The token can be an abbreviation and retains its supplied casing; it is not normalized to the full subcommand name. Subcommand arguments are not included.

A result of `-1` ends processing of the remaining subcommands. It can indicate failure, but is not a universal success/failure flag: individual subcommands determine their return value. Likewise, unchanged refnums do not prove that no property changed.

## Timing and limitations

The subcommand has already run when this hook is invoked. Its suppression result is ignored: it cannot veto that operation or stop the command's progress. There is no separate built-in display for the event.

A single `window` statement containing several recognized subcommands can trigger several events, including operations that leave the operative window unchanged. Selecting an operative window by a bare window name or refnum does not itself trigger this hook. An invalid token does not trigger it either. Bare `/window` displays information without generating this event.

The hook runs before the enclosing command restores status-update permission, checks channel associations, and updates windows. It is therefore not a promise that all resulting redraws or other final housekeeping have finished. Direct internal window operations and key bindings do not generate this event merely because they perform an action also available through `/window`; see [on switch_windows](switch_windows.md) for current-window changes.

## Example

Observe each recognized subcommand:

```text
on -window_command "*" (oldwin, newwin, subcmd) {
    echo Window subcommand $subcmd: $oldwin -> $newwin;
};
```

## History

This hook first appeared in EPIC4-1.2.0.

## Source references

- [source/window.c](../../source/window.c): `windowcmd` resolves subcommands, invokes each handler, reports its returned operative window, and performs final housekeeping.
- [source/commands.c](../../source/commands.c): built-in command registration and deferred-command processing.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
