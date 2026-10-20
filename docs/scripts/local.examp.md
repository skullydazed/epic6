# local.examp

## Purpose

`local.examp` is a sample script for installation-specific or personal customization. Copy it to a file named `local` and adapt its contents, or write a fresh `local` using it as a guide. The example filename is not automatically loaded as local configuration.

The [global](global.md) startup script finishes by checking `which(local)` and running `load local` if found. Put the file in a directory on [load_path](../set/load_path.md); discovery uses that script search path, not the shell's executable `PATH`. If no file is found, the step is silently skipped.

## Startup order

EPIC loads the selected readable personal startup file instead of `global`. When no such file is readable, it falls back to `global`, which loads the standard support scripts and then `local`. Quick startup with `-q` skips this automatic startup load.

Thus `local` does not unconditionally run before every user's startup file. Users with personal configurations opt into that load sequence by explicitly loading `global`, or can load `local` directly. Loading `global` within a personal startup file applies `local` at that point; commands following it can override the local configuration.

For example, a personal startup file can contain:

```text
load global
set beep off
```

The final setting runs after any discovered `local`. To load only a local configuration interactively, use `/load local`, ensuring its own dependencies are available.

## What the supplied example does

Loading the unmodified example:

- Prints a version announcement, blank lines, and sample bug-report text.
- Loads [guh](guh.md), defining user-information query helpers.
- Changes [client_information](../set/client_information.md) to sample text used by the supplied CTCP responder.
- Changes [status_user](../set/status_user.md) to an ircII help reminder.
- Defines `wasteland` to invoke `server` with the stored sample network hostname and port `6667`, and `undernet` as an alias for `wasteland`.

Those server aliases do not connect merely because the example is loaded; invoking one requests a connection under ordinary server-command rules. The sample is configuration code, not a template whose commands are inert. Replace its messages, settings, and aliases with the behavior wanted for your installation. Its `/help` reminder does not repair the retained help script's unavailable EPIC6 transport.

There are no script arguments, settings defined by the example itself, or function return values. Repeated loads repeat its output and setting assignments and redefine its aliases.

## Installation notes

The example comments intend `local` to hold custom changes separately from distributed files. The current `installscript` rule copies each file actually present in the source `script` directory by basename. The repository ships `local.examp`, not a standard `local`, so installing the normal distributed set does not create `local` from the example automatically. Do not interpret the comment as unconditional overwrite protection: a source file named `local` would also be copied by that rule.

## Source references

- [script/local.examp](../../script/local.examp): sample output, support load, settings, and server aliases.
- [script/global](../../script/global): conditional final `local` load.
- [source/irc.c](../../source/irc.c): personal startup selection, fallback, and quick startup.
- [source/functions.c](../../source/functions.c): `which` searches `load_path` by default.
- [Makefile.in](../../Makefile.in): `installscript` copies source script files by basename.
