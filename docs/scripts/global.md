# global

## Synopsis

```text
/load global
```

## Description

`global` supplies EPIC's default script configuration. It is loaded automatically when startup selects no readable personal startup file, rather than unconditionally for every session. It can also be loaded explicitly from your own startup configuration or at the command line.

The startup file is selected from `EPICRC`, then `IRCRC`, or otherwise the usual `~/.epicrc` path, falling back to `~/.ircrc` when `.epicrc` does not exist. The `-l` or `-L` command-line option overrides that selection. At load time, EPIC uses the selected file if readable; otherwise it loads `global`. Quick startup with `-q` skips this automatic loading. A readable personal file is used in place of `global`, so it must explicitly load any default scripts you want.

The startup loader runs before the initial automatic server connection. Loading `global` installs script behavior and changes client settings; it does not itself contain a server-connect command. Scripts it loads, especially a customized `local`, can have additional effects.

## Loaded scripts

The current file loads these in order:

| Script | Purpose |
| --- | --- |
| [builtins](builtins.md) | Compatibility commands, function wrappers, script-defined settings, and its own supporting scripts. |
| [history](history.md) | Script-based command history and related bindings. |
| [hold_mode](hold_mode.md) | Compatibility settings that apply hold behavior and intervals to windows. |
| [help](help.md) | The bundled help alias definitions. The current `help.irc` implementation still depends on obsolete DCC raw hooks; loading it is not a guarantee of working help retrieval in EPIC6. |
| [2.8script](2.8script.md) | Traditional IRC-client convenience aliases and display hooks. |
| [ambig](ambig.md) | Unknown-command handling that resolves a unique command-name prefix or reports ambiguity. |
| [newnick](newnick.md) | Nickname-conflict handling and its settings. |
| [python](python.md) | Python setup, helper aliases, and module-path configuration when the embedded Python interface is available. |

The commented-out `find_ssl_root_certs` load is not executed. `global` itself defines no `d+` or `d-` display aliases; its direct job is to load the listed scripts and the optional local configuration.

These are ordered loads, not a declaration of optional capabilities with automatic dependency resolution. For example, `builtins` attempts to load `mail`, whose current file calls `pyload`, before `global` loads the `python` script that defines `pyload`. In a fresh configuration lacking that alias, the earlier mail setup is not repaired merely by defining `pyload` later. Consult the supporting scripts' documentation for their current requirements and retained obsolete interfaces.

## Local customization

After the listed loads, `global` checks `which(local)`. If it finds a file through the current [load_path](../set/load_path.md), it runs `load local`. If none is found, it silently skips this step. This uses EPIC's script search path, not the shell's executable `PATH`.

Place installation-specific changes in that `local` file, or load `global` from your personal startup file before applying your own overrides. The repository's `script/local.examp` is an example, not a file that `global` automatically loads. Commands in a discovered `local` run after the standard load sequence and can override its aliases, hooks, bindings, and settings.

## Effects and limitations

The dependent scripts initialize settings and definitions, so loading `global` again can reset values or replace handlers. It is not an operation for inspecting the configuration and has no return value or guaranteed success summary. Ordinary load diagnostics identify missing or unreadable scripts; a failed helper or unavailable runtime does not provide a rollback of earlier changes.

An explicit load does not change the startup-selection rules or make the selected personal file load automatically afterward. Where scripts define the same alias or hook, load order and the underlying hook rules determine the result.

## Example

To begin a personal startup file with the default configuration, then override a setting:

```text
load global
set beep off
```

This runs the default load sequence, including any discovered `local`, before applying the final setting.

## Other notes

The default configuration has evolved through contributions from many authors; it is not the work of a single creator.

## Source references

- [script/global](../../script/global): actual load order and conditional `local` load.
- [source/irc.c](../../source/irc.c) and [include/irc.h](../../include/irc.h): startup selection, environment variables, command-line overrides, and startup timing.
- [source/functions.c](../../source/functions.c): `which` defaults to `load_path`.
- [script/builtins](../../script/builtins), [script/mail](../../script/mail), and [script/python](../../script/python): supporting load order and `pyload` definition.
- [script/help.irc](../../script/help.irc): legacy help transport references.
