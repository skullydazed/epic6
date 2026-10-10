# stub

## Synopsis

```text
/stub alias <name>[,<name>...] <filename> [<filename>...]
/stub assign <name>[,<name>...] <filename> [<filename>...]
```

## Description

`stub` creates a load-on-demand alias or global script assignment. When a stubbed symbol is looked up, EPIC deletes the stub, synchronously [loads](load.md) its stored file or files, then repeats the lookup. The loaded script should define the real alias or assignment before that lookup resumes.

This can reduce startup work by deferring features until they are used. Loading executes the whole script, including its side effects; it is not limited to importing the requested definition.

## Arguments

The type is `alias` or `assign`, case-insensitive. Prefixes are accepted, with `a` selecting `alias`; use `assign` or an unambiguous longer prefix for an assignment stub.

Names form one comma-separated word, without spaces around commas. They are case-insensitive and support ordinary bracket-to-dot name conversion. Supply names rather than expanded values. Assignment stubs are global; a visible local variable can hide them. `function_return` cannot be stubbed as an assignment, and invalid assignment names produce a diagnostic.

The remaining nonempty text is stored as the argument string for a later `load` invocation. Multiple filenames are supported. Loader options, path searching, source encoding, and filename restrictions follow `load`; filenames containing spaces are not grouped by quotes in its parser. File existence is not checked when the stub is created.

The triggering command's or function's arguments are not passed to the loader: the autoload invocation uses an empty argument context. Once the real alias is found, its original invocation proceeds normally. Explicit loader arguments, if needed, must use `load`'s supported `-args` form in the stored text.

Missing type, names, or file text produce diagnostics and usage. Creating a stub prints an `Alias NAME stubbed to file ...` or `Assign NAME stubbed to file ...` message. The command has no script return value and requires no IRC connection.

## Lookup, replacement, and failures

A stub replaces the corresponding existing alias body or global assignment value. Stubs are normally consumed once. When a loaded file defines several stubbed names, those definitions replace the remaining stubs too; there is no separate file cache, so a still-stubbed name can later load the same file again.

A lookup is broader than invoking a command or reading a variable in ordinary script text. Global symbol lookup checks assignment and alias stubs sharing that name, so looking up one kind can also trigger the other. Some inspection operations can load stubs. Installing an alias definition or another stub also uses lookup and can trigger the old stub before replacing it. A normal global assignment replaces its assignment stub directly.

If the file cannot be loaded, or does not define the requested item, the deleted stub is not restored and a later reference does not automatically retry it. Lookup continues through the normal undefined-symbol or fallback behavior. Reinstall the stub if you want another attempt.

While one stub's files are being loaded, a global recursion guard prevents another stub from starting a nested autoload. Such a nested lookup still deletes its stub and continues lookup **without loading its files**. Scripts loaded through stubs should define their dependencies directly or load them explicitly, rather than depending on nested stubs.

When created inside a [package](package.md) context, stubs carry the applicable package metadata. Definitions made by the loaded script use that script's load/package context. Use the ordinary [alias](alias.md), [assign](assign.md), or [unload](unload.md) mechanisms when replacing or removing definitions.

## Examples

Load `foobar.irc` when `/foo` is first looked up:

```text
stub alias foo foobar.irc
```

Load `footoo.irc` when global `foo` or `bar` is accessed:

```text
stub assign foo,bar footoo.irc
```

The first file should define alias `foo`; the second should define assignments `foo` and `bar`. Defining both assignments during the first load avoids another load when the other variable is accessed.

## Source

Registration is in [source/commands.c](../../source/commands.c). `stubcmd()`, stub storage, `lookup_symbol()`, `unstub_variable()`, and `unstub_command()` are in [source/alias.c](../../source/alias.c). Loading and package contexts are in [source/commands.c](../../source/commands.c), and name canonicalization is in [source/ircaux.c](../../source/ircaux.c).
