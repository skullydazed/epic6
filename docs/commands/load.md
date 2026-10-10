# load

Execute EPIC script commands from one or more files.

## Synopsis

```text
/load [-std|-pf] [-encoding encoding] file [file ...]
/load [-std|-pf] [-encoding encoding] -args file [arguments...]
```

## Description

`load` reads and executes script files in order. EPIC also uses this mechanism for its startup script, normally `~/.epicrc` when available, unless startup loading is disabled with `-q` or another startup file is selected. The bundled [script directory](../../script/) contains examples and reusable scripts.

Names are searched using the colon-separated `/set load_path`. Absolute paths, tilde paths, and relative paths containing directories are accepted. A name containing `/` is first tried relative to the current directory or as its explicit path; if that fails, the load path is searched. A bare name is searched through the load path. Even an explicit path requires `load_path` to be set; otherwise EPIC reports that it is unset.

Files are loaded synchronously. Script commands can change client state, define aliases and hooks, and initiate server requests; loading a file does not merely import definitions. There is no successful-load return value. Missing or unreadable files produce diagnostics, and subsequent files in the invocation can still be tried. Empty files execute nothing.

## Loaders and options

The default loader is `-std`, the legacy line-oriented loader. It handles line boundaries and brace grouping, inserts command separators as needed, skips lines beginning with `#` after whitespace, supports trailing-backslash continuation, and processes C-style comments according to `comment_hack`. Its special line and brace rules differ from ordinary script-block parsing.

The `-pf` loader expects preformatted script code: commands must have explicit semicolon separators, and line breaks do not terminate commands. It removes leading whitespace and whole-line `#` comments, joins the remaining text, and executes it as a command block in a new local scope. Choose the loader expected by the script rather than assuming the formats are interchangeable.

| Option | Effect |
| --- | --- |
| `-std` | Use the standard loader for following files. |
| `-pf` | Use the preformatted loader for following files. |
| `-encoding encoding` | Select the source encoding used when the file is not valid UTF-8. |
| `-args` | Treat the next word as the final file name and all remaining text as its arguments. |

Options are case-insensitive and recognized by prefix. Prefer full names. Put loader and encoding options before `-args`: once it has selected the next file, following text is arguments rather than further options or files. Filenames and encoding names use whitespace-delimited parsing without double-quote grouping, so names containing spaces are not supported by this command's argument parser.

With `-pf -args`, the supplied arguments become the script's `$*` and positional expandos. The standard loader explicitly ignores these arguments and executes statements without that argument context. Without `-args`, files do not receive trailing filenames as arguments.

The default source encoding comes from [encoding](encoding.md) rules for `scripts`. Valid UTF-8 files are used directly even when a different encoding was selected; otherwise EPIC attempts conversion through iconv and announces it. Conversion support depends on the available iconv encodings. This option is not a request to decode valid UTF-8 bytes using another character set.

## Compression, scope, and restrictions

EPIC can find compressed forms when the base name is absent, trying `.gz`, `.Z`, `.z`, and `.bz2`. It can also load explicit supported compressed filenames. Decompression uses external `gunzip`, `uncompress`, or `bunzip2`/`bunzip` programs found in the executable path; unavailable decompressors or failures prevent loading. Compressed scripts therefore require host process facilities and the appropriate program. This is not archive extraction.

The PF loader refuses a file marked executable unless it begins with `#!`. The standard loader warns about an executable file without that marker but does not impose the same refusal. EPIC executes script text itself; it does not run the file as an operating-system program.

Load nesting is limited to ten active levels. A [return](return.md) in a loaded script ends that file, with the load boundary clearing the return condition. It does not cancel later filenames in the same invocation. Normal output is suppressed during loading, and status updates are postponed; explicit output commands and script changes to display settings can still affect what is shown.

Nested loads inherit the enclosing load's package context. Scripts can change it using [package](package.md) and [subpackage](subpackage.md), which label definitions for package management; inheritance does not prefix every alias or variable name automatically. The load context is removed afterward, while definitions created by the script remain. PF-local variables disappear with its execution scope; standard statements do not create that PF scope.

The C built-in is always registered. [which](which.md) shares its handler but defaults to displaying a resolved filename instead of executing it; `load` itself defaults to the standard execution loader.

## Examples

```text
/load myscript
/load -pf ~/scripts/example.epic
/load -pf -args example alice bob
/load -encoding ISO-8859-1 oldscript
```

A simple PF script can contain:

```text
@ local_count = 3;
echo Arguments: $*;
```

The files in the commands must exist, and their syntax must match the selected loader.

## Source

Registration, options, loader implementations, nesting, and package context are in [source/commands.c](../../source/commands.c). File search, tilde expansion, decompression, and file reading are in [source/ircaux.c](../../source/ircaux.c). Startup loading is in [source/irc.c](../../source/irc.c), encoding selection is in [source/recode.c](../../source/recode.c), and definition package metadata is in [source/alias.c](../../source/alias.c).
