# save script

## Synopsis

```text
/load save
/save <-alias|-all|-assign|-bind|-notify|-on|-server|-set> [-append] <path>
```

## Description

The `save` command writes selected client configuration as script commands to a file so it can be restored later by [load](../commands/load.md). It is a script-provided replacement for the former built-in save command, not a complete session snapshot.

Choose exactly one category, or `-all`, as the first argument. The optional `-append` must follow that category; the remainder is the required path. There is no default filename, default category, interactive overwrite question, or support for combining several category flags in one call.

Normally the script attempts to delete the destination before opening it for writing. With `-append`, it leaves the existing file and appends commands. It does not create missing parent directories, make a backup, or perform an atomic replacement.

## Categories

| Flag | What the implementation writes |
| --- | --- |
| `-alias` | `alias` commands for current user aliases, containing their bodies. Formal parameter lists and package metadata are omitted. |
| `-assign` | `assign` commands for current global variables, excluding its `save.fd` descriptor variable. Local variables are not saved. |
| `-bind` | Bindings from the current keymap, excluding bindings whose action is exactly `self_insert`. It does not save every keymap. |
| `-notify` | Iterates `$notify()`. With the supplied EPIC6 [notify](notify.md) script this returns empty, so no monitored nicknames are written. |
| `-on` | Registered event hooks serialized by `hookctl`, including their hook syntax and command bodies. |
| `-server` | `server -add` commands containing server-reported name, port, password, nickname, group, and protocol. |
| `-set` | `set` commands for all currently registered built-in and script-provided settings. |
| `-all` | All categories above, in the order alias, assign, bind, notify, on, server, set. |

`-all` does not include windows, scrollback, active connections, timers, file descriptors as usable resources, process state, or server-maintained MONITOR lists. Global variables can contain runtime bookkeeping, but saving their text does not reconstruct the resources they refer to.

## Restoring and limitations

Load the saved file explicitly to replay its commands. Restoration is not automatic and is not a rollback: it changes the current client and can invoke callbacks, replace definitions, add server entries, or cause other effects through the saved commands. Required scripts or setting definitions must be available when the file is loaded.

The output is not a fully faithful serialization. Alias bodies are saved without their formal argument lists, so aliases depending on named parameters may not work after restoration. Script-defined setting types and callback definitions are not recreated by their saved values. Alias, variable, and hook package ownership is not restored. Self-insert bindings omitted from the file cannot undo a different binding already installed in the receiving client.

Server records save only the six fields listed above, not every current server-description attribute. The name used is `itsname`, which can be empty or differ from the originally configured connection address. Passwords are written as plain text, and settings or globals can contain additional credentials. The script applies ordinary file creation permissions without a dedicated permission-hardening step.

Values are interpolated into command lines without a general escaping or multiline-serialization mechanism. Unusual quoting, embedded newlines, or other script-significant text may not replay as intended. Review a generated file before using it as a startup configuration, especially after saving runtime variables or aliases from many packages.

## Errors and output

Invalid category or missing path prints usage. Failure to open the destination does not produce a dedicated failure message from this wrapper. Individual unlink, write, and close results are not checked. If the initial deletion fails but append-mode opening succeeds, the file can be appended to even without `-append`; if deletion succeeds and opening later fails, the old file is already gone.

After invoking the category writer, it prints `Settings saved to <path>` before closing the file. This is not verification that every line was written or can be loaded successfully. The script provides no function-return status, progress log, validation pass, or recovery copy. It uses a shared `save.fd`, so do not run overlapping saves or call its internal category aliases independently.

No external program, IRC request, or optional platform feature is needed for saving. A writable local path is required. The script defines no settings and does not itself arrange periodic saves.

## Examples

```text
/load save
/save -set ~/epic-settings.irc
/save -bind -append ~/epic-settings.irc
```

The first save replaces the destination with setting commands; the second appends bindings. After reviewing it and loading any required setting definitions, replay it with:

```text
/load ~/epic-settings.irc
```

## History

The script was written by BlackJac for EPIC5 to replace the built-in save command. The retained source is version `1.1.2006.12.11.1`.

## Source references

- [script/save](../../script/save): argument order, category writers, unlink/open sequence, and completion message.
- [script/notify](../../script/notify): `$notify()` is an empty placeholder.
- [source/alias.c](../../source/alias.c): `symbolctl` exposes alias bodies and argument lists separately.
- [source/keys.c](../../source/keys.c): `bindctl(map)` enumerates the current keymap and `sequence get` returns action plus arguments.
- [source/hook.c](../../source/hook.c): hook serialization through `hookctl`.
- [source/server.c](../../source/server.c): server fields read by `save.server`.
- [source/functions.c](../../source/functions.c) and [source/files.c](../../source/files.c): unlink, append-mode `open(... W)`, and newline-terminated writes.
