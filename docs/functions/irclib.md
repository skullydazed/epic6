# irclib function

## Synopsis

`$irclib()`

## Technical

Returns the IRC library directory selected when the client starts. No arguments are required; supplied arguments are ignored. The value remains unchanged for the running client.

At startup, the `IRCLIB` environment variable overrides the compiled installation directory. EPIC appends `/` to that environment value, even if it already ends with `/`; the normal compiled value also has a trailing slash. The path is copied without expanding `~`, resolving it to an absolute path, or checking that it exists. An empty environment value therefore produces `/`.

This directory supplies the `%s` portion of the default [set](../commands/set.md) `load_path`, whose standard template is `~/.epic:~/.irc:%s/script:.`. A separate `IRCPATH` environment variable overrides the whole startup load path without changing `$irclib()`. Later changes to `load_path` or to environment variables through [setenv](../commands/setenv.md) likewise do not change the stored library directory.

## Practical

Use this to locate installed scripts independently of a user's current script search path. The directory is configurable by the user, so it does not guarantee that a file is the originally installed version. The function returns the selected runtime path, without separately exposing the compiled default when overridden.

## Returns

The stored directory string, without added quoting or validation. The query produces no diagnostic and performs no filesystem operation.

## Examples

```irc
load $irclib()/script/motd
```

This explicitly selects the `motd` script beneath the chosen library directory instead of searching `load_path`. The extra slash is harmless for an ordinary Unix directory path; loading still depends on the file existing and being readable. Paths containing spaces require the quoting supported by [load](../commands/load.md).

## Source

Registration and direct return: [source/functions.c](../../source/functions.c). Startup `IRCLIB` and `IRCPATH` handling: [source/irc.c](../../source/irc.c). Default search-path template: [include/config.h](../../include/config.h). Compiled directory definition: [source/Makefile.in](../../source/Makefile.in).
