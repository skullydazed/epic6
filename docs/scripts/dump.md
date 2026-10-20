# dump

## Synopsis

```text
/load dump
/dump [all|alias|array|bind|channel|hook|on|timer|var|window|wipe ...]
```

The [builtins](builtins.md) script normally provides an on-demand stub, so `/dump` can load this script automatically.

## Description

Mass-delete selected parts of the client's current state without restarting EPIC. This can clear script definitions before reloading a configuration. It does not edit startup files or save a backup, and there is no confirmation prompt or built-in undo. Options can be combined and run from left to right.

With no arguments, `all` removes user aliases, installed hooks, and global script assignments. It does not mean every kind of client state: arrays, key bindings, channels, timers, windows, and settings are outside that default selection. In particular, it does not completely reverse everything a startup script may have configured.

This is a script-defined command. Most operations are local, but channel departure and window deletion can send IRC requests or invoke other scripts' hooks. Their server-side effects remain subject to connection state and server behavior.

## Options

| Option | Current behavior |
| --- | --- |
| `alias` | Delete global user aliases, including the `dump` dispatcher itself. Preserve the `dump.*` helpers until deferred cleanup removes them after the current command sequence. C built-in commands and functions remain. |
| `array` | Delete all named arrays returned by `getarrays(*)`. |
| `bind` | Invoke [bind -defaults](../commands/bind.md), resetting key bindings to factory defaults rather than leaving every key unbound. |
| `channel` | Invoke `join 0`, intended to leave channels on the current server. This does not iterate over all servers. |
| `hook` or `on` | Remove installed hooks returned by `hookctl(list hooks)`. It does not remove C default handling or separately stored implied hooks. |
| `timer` | Attempt to delete all listed timers through `timerctl(delete ...)`. Internal timers with C callbacks are protected by that interface and remain. |
| `var` | Delete global script assignments returned by `symbolctl(pmatch assign *)`; active local variables and built-in settings are not this assignment list. |
| `window` | Attempt to select and kill every window except the one current when the list is collected, then apply a basic configuration to the window current at the end. See below. |
| `all` | Run `alias`, `hook`, and `var`, in that order. This is the default. |
| `wipe` | Attempt `alias`, `array`, `bind`, `channel`, the obsolete `dcc` operation, `hook`, `timer`, `var`, and `window`, in that order. It is broader than `all`, but not a reliable complete reset. |

Options are case-insensitive. The script matches `alias*`, `array*`, `bind*`, `channel*`, `hook*`, `on*`, `timer*`, `var*`, and `win*`, so plural forms and longer words with those prefixes are accepted; short prefixes such as `a` are not. `all` and `wipe` must match those words. An unknown option prints `Dump what?`; its diagnostic interpolates an unrelated variable named `choice`, so the displayed unrecognized value can be empty or misleading.

## Effects and limitations

The channel helper uses `^join 0`, not `//join 0`: display suppression does not bypass a script alias. A replacement `join`, such as one from another startup script, can reinterpret `0` instead of sending the built-in `JOIN 0`. Use the underlying [join](../commands/join.md) documentation when you need predictable departure behavior.

The window helper uses [refnum_or_swap](../window/refnum_or_swap.md) followed by `kill`. Hidden or unswappable windows, screen constraints, and other kill restrictions can prevent the intended cleanup. Later operations can still run after some failures, so it should not be treated as an atomic operation guaranteed to preserve exactly one particular window. Window-deletion hooks can also run before hooks are cleared when `window` is used separately.

Its final window command requests single status, fixed off, hold mode off, all lastlog and routing levels, no name, notification off, all notification levels, number `1`, skip off, and swappable on. It does not reset every window property, clear all stored output, or restore every global setting. The attempted `name -` removes a name; renumbering can exchange refnums with a surviving window.

The retained `dcc` option matches `dcc*` but calls the removed `/dcc closeall`. It does not close current sockets or restore the former DCC subsystem. `wipe` includes that stale step and can produce an unknown-command diagnostic. Other steps are not rolled back if any operation fails.

Each helper prints a `Dumping your ...` heading, not a count or a verified success report. Removing aliases can make later `/dump` invocations unavailable until the script or its stub is loaded again. Deleting variables and hooks also removes support used by other loaded scripts, without unloading every setting or timer they installed.

## Examples

```text
/dump on
/dump array timer
/dump all
```

These respectively remove installed hooks, delete named arrays and script timers, or perform the default alias/hook/global-variable deletion. Use them only when that loss of current state is intended. Reload the needed configuration afterward; no files were changed by the deletion itself.

## Source references

- [script/dump](../../script/dump): dispatcher, option order, helpers, deferred alias cleanup, and final window configuration.
- [script/builtins](../../script/builtins): on-demand `dump` stub.
- [source/functions.c](../../source/functions.c) and [source/alias.c](../../source/alias.c): filtering, symbol enumeration, and alias/assignment deletion.
- [source/hook.c](../../source/hook.c): installed-hook enumeration and removal.
- [source/timer.c](../../source/timer.c): protected internal callback timers.
- [source/keys.c](../../source/keys.c), [source/commands.c](../../source/commands.c), and [source/window.c](../../source/window.c): binding defaults, joins, selection, and window deletion.
