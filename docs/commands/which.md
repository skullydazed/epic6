# which command

## Synopsis

```text
/which file [file ...]
/which -args file
```

## Description

`which` looks for files using the colon-separated [`load_path`](../settings/load_path.md) setting, as [`load`](load.md) does, and displays each resolved filename. Use it to see which script a normal load-path search would select. The effective search directories depend on your configured `load_path`; inspect them with `/set load_path` rather than assuming a fixed library or home-directory layout.

A bare filename is searched through the load path. A filename containing `/` is first tried as its explicit path or relative to the current directory, then through the load path if that fails. Tilde paths are expanded. Even an explicit path requires `load_path` to be set; otherwise the handler reports `LOAD_PATH has not been set`. Names are whitespace-delimited, without quote grouping, so this argument parser does not support filenames containing spaces.

The default `which` loader only displays the path and does not execute script commands. However, the shared loader opens and reads the file before displaying its name, including decompression and source-encoding conversion when applicable. Empty files produce no resolved-path output. Missing or unreadable files produce diagnostics; later files can still be tried. Compressed-file lookup and external decompressor requirements are the same as `load`.

Successful paths are displayed through the diagnostic `yell` mechanism, so a yell hook can handle or suppress them. This command does not return a filename as a function value; see the [`which` function](../functions/which.md) for a function interface.

## Options and restrictions

The handler accepts the same options as `load`. `-args` treats the next word as the final filename and ignores subsequent text in the default path-display mode. `-encoding encoding` controls conversion of non-UTF-8 file contents; otherwise the `scripts` encoding rule is used.

**`-std` and `-pf` switch to execution loaders.** Supplying either to `which` causes following files to be executed as scripts, just as with `load`; they are not alternate search modes. Options are case-insensitive and recognized by prefix. Use bare `which file` for path inspection without script execution.

The command is always registered and requires no IRC connection. It uses the load nesting limit, temporarily suppresses ordinary output, and postpones status updates while processing files. It does not refresh or modify the load path.

## Examples

```text
/set load_path
/which myscript
/which ~/scripts/myscript
```

The first displays the current search-path setting. The other commands display the selected path when the file can be opened and has nonempty contents; they use the default display loader.

## Source

[source/commands.c](../../source/commands.c) registers `which`, selects `loader_which()` in `load()`, parses shared options, and reads files before dispatching the loader. [source/elf.c](../../source/elf.c) implements file opening and decompression; [source/ircaux.c](../../source/ircaux.c) implements path lookup.
