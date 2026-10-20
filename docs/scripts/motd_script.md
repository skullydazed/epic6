# motd script

## Synopsis

```text
/load motd
```

## Description

This script displays a local installation's message of the day when its file is newer than the user's timestamp marker. It is independent of the IRC server's [motd](../commands/motd.md) command: it reads local files rather than requesting the server MOTD.

The administrator supplies `ircII.motd` in the directory returned by `$irclib()`. The script compares that file's modification time with `~/.ircII.motd`. If the local MOTD has a usable nonzero timestamp and is newer than the marker, it opens the text through the [less](less.md) pager and runs:

```text
touch ~/.ircII.motd
```

The external command is launched through `exec`. The marker's contents are irrelevant; its modification time records when the script last attempted to present the file. A missing marker produces an empty timestamp, normally allowing an existing newer MOTD to display. A missing or inaccessible source file normally produces no display or marker update.

## Startup use and requirements

Put `load motd` in your [local configuration](local.examp.md), or load it explicitly from a personal startup file if this behavior is wanted. The current default `global` and supplied `local.examp` do not automatically load it. The comparison happens when the script runs, not continuously; a changed file is shown on a later load, such as a later startup that includes it.

The script loads `less` itself. That pager uses `winnum` and `winsize` from [builtins](builtins.md), so those definitions must already be available. The client must be able to read the source, and the external `touch` command and configured exec shell must support updating the marker in the user's home directory. `$irclib()` is the configured library directory, including an `IRCLIB` environment override when selected during client startup; it is not a search through `load_path` for the MOTD file.

## Effects and limits

The marker command is launched after the pager's first call returns, even if the user is still being prompted for later pages or later quits reading. It therefore does not prove the complete message was read. A pager or marker-write failure is not checked or retried by this wrapper. If updating the marker fails, the MOTD may be shown again on subsequent loads.

Only modification times are compared, not file contents. Changed text with an unchanged or older timestamp is not detected, and a marker timestamp newer than the source prevents display. Loading overwrites ordinary script variables named `date` and `fdate`.

The inherited pager limitations still apply, including a single shared paging state, obsolete continuation-setting references, and unreliable descriptor cleanup when quitting. See [less](less.md) for details. There are no script-specific arguments, settings, polling interval, or function return value. A normal load with nothing new is silent apart from any dependency-load diagnostics.

## Example

In a local startup configuration that already supplies the default support functions:

```text
load motd
```

This checks the installation's local MOTD once each time that configuration runs. It does not suppress or replace messages sent by an IRC server during registration.

## History

This script first appeared in EPIC4pre1.002. The retained source is marked as 1997 work by hop.

## Source references

- [script/motd](../../script/motd): timestamp comparison, pager load, and marker command.
- [script/less](../../script/less): local file display and paging behavior.
- [source/functions.c](../../source/functions.c): `ftime` returns modification time or empty on failure; `irclib` returns the library directory.
- [source/irc.c](../../source/irc.c): library-directory initialization and `IRCLIB` override.
- [source/exec.c](../../source/exec.c): external command execution.
