# cd command

## Synopsis

```text
/cd
/cd <path>
```

## Description

`cd` changes the current working directory of the EPIC process. With a path, it attempts to change to that directory. Without a path, it simply displays the current directory; it does not automatically change to your home directory.

After either form, including a failed change, the command normally prints `Current directory: path`. If EPIC cannot obtain the current directory, it prints `The current directory is invalid.`

Relative paths are resolved from the current working directory. Home-directory expansion is supported: `~` or `~/...` refers to EPIC's recorded home directory, while `~user` or `~user/...` uses that user's system account information. The path is canonicalized with `realpath()`, resolving symbolic links, `.` and `..`, before changing directories. It must exist, and the operating system must permit changing into it.

Only one path argument is read. Use double quotes around a path containing spaces; the surrounding quotes are removed. Any further arguments are ignored. There are no options, shell-style glob expansion, or special previous-directory syntax: `-` is an ordinary path name.

Changing directories affects the whole client process, including later operations that interpret relative file paths. It does not change the working directory of your parent shell. The command is local, is available in every build, and requires no IRC connection.

## Errors

If home-directory expansion or canonicalization fails, the command prints `CD: path contains an invalid directory` and does not change directories. If `chdir()` fails, it prints `CD:` followed by the operating system's error text, and the working directory remains unchanged. An existing regular file can pass canonicalization but then fail because it is not a directory.

## Examples

To display the current directory, change to your home directory, or move to the parent directory:

```text
/cd
/cd ~
/cd ..
```

To change to a directory containing spaces:

```text
/cd "/tmp/my files"
```

The example directory must already exist.

## History

The `cd` command first appeared in ircII.

## Source

See [source/commands.c](../../source/commands.c) for the registration and `cd()` handler, [source/ircaux.c](../../source/ircaux.c) for `normalize_filename()` and `expand_twiddle()`, and [include/words.h](../../include/words.h) for the quoted argument parser used by the handler.
