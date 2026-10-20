# man script

## Removed help interface

The `man` script provided a man-like topic lookup for the former sectioned EPIC help files. Its alias remains in `script/man`, but the `help_path` setting and old file-browser `help` interface it relies on are retired in EPIC6. Loading the script does not restore those facilities or provide a working browser for the new documentation.

This is not a wrapper around the operating system's `man` executable. It neither launches a subprocess nor reads system manual-page databases.

## Retained syntax and behavior

```text
/load man
/man <topic>
```

With a first argument, the retained alias tests filename globs of the form:

```text
<help_path>/<section>*/<topic>
```

It tries sections `1`, `2`, `3`, `4`, `5`, `6`, `7`, `8`, then `n`, stopping at the first match. This is a fixed sequence of directory patterns, not a recursive search of all subdirectories or a search of file contents. Wildcards in the first topic word participate in filename matching. Additional arguments are forwarded to the former help command rather than used in the filename test.

On a match, it calls `help <section> <original arguments>` and then `help -wait`. These are old help-browser operations. The alias itself does not display the matched file, verify that `help` accepted the request, or return a topic string. With no arguments or no match, it is silent.

There is no current C `help_path` setting. Without a user-defined value, its expansion is empty and the retained glob begins at filesystem root rather than at a documentation directory. Defining that variable manually can change the glob location, but does not reimplement the missing browser.

The later retained [help script](scripts_help.md) accepts page text instead of the old section/wait protocol, and its C `connect()` dependency unconditionally returns empty. Thus its presence does not make either of these calls work as the old browser expected. A separately supplied custom `help` alias would define its own behavior.

Remove obsolete `man` setup when migrating documentation access. Use the documentation pages directly outside the retired in-client file-browser interface. The retained alias has no options, automatic help message, or current replacement configuration for this purpose.

## Other notes

Written by Dawid Kuroczko; the source is marked 1997.

## Source references

- [script/man](../../script/man): section order, filename patterns, and old `help` calls.
- [script/help.irc](../../script/help.irc): later incompatible help wrapper.
- [source/vars.c](../../source/vars.c) and [source/commands.c](../../source/commands.c): current registrations omit the old help-path setting and built-in help command.
- [source/functions.c](../../source/functions.c): file globbing and the empty-returning `connect` placeholder.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of the old help-file support.
