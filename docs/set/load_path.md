# set load_path

## Synopsis

```text
/set load_path [<directory>[:<directory>...]]
/set -load_path
```

With no value, [set](../commands/set.md) displays the current path. The value is a colon-separated search list; setting it replaces the list. `/set -load_path` unsets it.

## Description

Used by [load](../commands/load.md), [which](../commands/which.md), and [which()](../functions/which.md) to locate script files. Directories are searched in order and the first matching candidate is used. Tilde forms are processed by filename normalization.

A filename containing `/`, such as `./myscript` or `~/myscript`, is first tried directly after normalization. If that candidate is not found, path searching can still follow. A bare filename is searched through the configured list; the current directory is not implicitly searched first unless represented in that list. Use an explicit `.` entry for it.

Do not treat empty path components as the shell convention for the current directory: the current search implementation joins each component with `/` and the filename, so an empty component produces a root-relative candidate. Colon-containing directory names cannot be represented as one component in this format.

## Startup default

At startup, EPIC copies the `IRCPATH` environment variable when it exists. Otherwise the compiled default is:

```text
~/.epic:~/.irc:<irc-library>/script:.
```

The library prefix comes from `IRCLIB` in the environment or the compiled installation location. This initialization stores an ordinary `load_path` value. `IRCPATH` does not continue overriding later assignments during startup script loading or afterward.

Changing the setting affects subsequent searches; it does not reload scripts or change the process working directory. Unsetting it causes the loader to report `LOAD_PATH has not been set`, even before trying a supplied filename. A configured directory list does not guarantee that a file is readable or valid script text. Changing it sends no IRC request.

## Examples

Set an explicit path including your home directory, an example installed library, and the current directory:

```text
/set load_path ~:/usr/local/irc/script:.
```

Append a personal directory while preserving the existing value:

```text
/eval set load_path ${load_path}:~/my_irc
```

Use the actual installation directory for your setup. Entries earlier in the list take precedence over same-named files in later entries.

## Source references

- [source/irc.c](../../source/irc.c): startup chooses `IRCPATH`, `IRCLIB`, and the compiled search-list template.
- [include/config.h](../../include/config.h): `DEFAULT_IRCPATH` template.
- [source/commands.c](../../source/commands.c): loader reads the setting and passes it to `uzfopen`.
- [source/ircaux.c](../../source/ircaux.c): `path_search` implements direct slash-containing filename checks and ordered component searches; `uzfopen` uses it.
- [source/functions.c](../../source/functions.c): `function_which` uses the configured script path.
- [source/vars.c](../../source/vars.c): string registration and unset handling.
