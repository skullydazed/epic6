# files

## Synopsis

```text
/load files
/cat <file>
/grep <pattern> <file> [<file>...]
/log_it <file> <text>
/exclude <file> <regular-expression>
/filter_file <file> <command>
$slurp_file(<file>)
$randomread(<file-or-glob> ...)
```

## Description

This script demonstrates EPIC's built-in file-access functions through script aliases. It does not run the Unix `cat` or `grep` programs, and its options and matching rules differ from those tools. Loading the script defines the helpers; it does not open or modify files until a helper is called.

| Helper | Behavior |
| --- | --- |
| `cat <file>` | Open the first named file for reading and display each line with `echo`. Additional filename arguments are ignored. |
| `grep <pattern> <files...>` | Read each named file and display matching lines as `filename: line`. It uses case-insensitive EPIC wildcard matching with `*` added around the pattern, not regular expressions. Matching searches the line's words through `match`. |
| `log_it <file> <text>` | Open the file in EPIC's `W` mode, append the remaining argument text plus a newline, then close it. This is independent of session logging. |
| `slurp_file(<file>)` | Return the file's lines concatenated without their newline separators; this does not preserve the original file bytes or line boundaries. |
| `filter_file <file> <command>` | For each read line, evaluate the supplied command followed by that line as arguments. This executes commands, not a text-only filter producing a separate output file. |
| `exclude <file> <expression>` | Attempt to replace the file with the lines that do not match a case-insensitive extended regular expression. See the replacement risks below. |
| `randomread(<files-or-globs>...)` | Expand file patterns, select one random line from each file by counting and seeking, and return the selected lines joined with spaces. As a command, `/randomread ...` echoes that combined result. |

There are no script-specific settings, Unix-style flags, or structured success results for the command helpers. Only `randomread` expands filename globs itself; `grep` iterates its filename arguments without doing glob expansion.

## File handling and limits

Paths are local to the client's machine and ordinarily relative to its working directory. The aliases mostly substitute `$0` into unquoted file-function calls, so filenames containing spaces are not handled reliably. Use simple filenames. File reads strip newline terminators and can recode invalid UTF-8 using EPIC's `scripts` encoding rules; these are text helpers, not binary copy routines.

EPIC's `open(file W)` means append, despite the `W` spelling. `log_it` therefore creates a missing file or appends to an existing one rather than replacing its previous content. It writes one logical line with a newline supplied by `write`.

The helpers do not check open, read, write, close, unlink, or rename results. An invalid read handle reports end-of-file through `eof`, so some read helpers simply produce no output on open failure. Other failures are not reliably distinguished from empty input. The `while (!eof(...))` loops can process an extra empty result at end-of-file. Do not interpret their completion as a verified successful copy or rewrite.

`randomread` scans each whole file before seeking to its selected line. It is not the same algorithm as the built-in byte-position-based `randread`. Empty files, failed opens, or non-seekable inputs are not validated. Its line count uses `fskip`, whose EOF handling can omit a final line without a newline terminator. Multiple selected lines are joined as text, without an unambiguous record boundary.

Several aliases use ordinary assignments such as `fd`, `line`, and `x`, so they can overwrite script variables of those names. `slurp_file` accumulates the entire result in memory. `filter_file` deliberately evaluates its command template; use it only when that command execution and its side effects are intended.

## Replacing a file with exclude

`exclude` compiles its remaining arguments as a regular expression, opens the source for reading, and opens `<file>.new` in append mode. It writes lines when `regexec` returns a nonzero result, normally meaning no match. It then closes both files, frees the expression, unlinks the original path, and renames `<file>.new` to the original path.

This sequence has no failure checks, backup, or rollback. Existing `<file>.new` contents are appended to and can become part of the replacement. Invalid expressions and read or write failures are not checked before unlinking the original. A failed rename can leave the original pathname missing. The replacement can also lose the last unterminated line and does not preserve original file metadata. Use the helper only on disposable copies; it is a demonstration rather than a reliable editing utility.

## Examples

```text
/load files
/cat notes.txt
/grep warning notes.txt old-notes.txt
/log_it notes.txt Reviewed the configuration
/echo $randomread(quotations.txt)
```

These display a file, search two files, append a line, and display one selected line from a file. No IRC request is sent by these helpers themselves; any command supplied to `filter_file` determines its own effects.

## Other notes

Written by Jeremy Nelson as examples of EPIC's file functions. They favor illustrating the scripting interface over the performance and error handling of dedicated file tools.

## Source references

- [script/files](../../script/files): helper definitions and unchecked replacement sequence.
- [source/functions.c](../../source/functions.c): `open` maps `W` to append; `match`, `glob`, `regcomp`, and `regexec` supply matching behavior.
- [source/files.c](../../source/files.c): line reading, newline removal, text recoding, EOF, seeking, and writes.
- [source/if.c](../../source/if.c): `fe` mapping joins the selected `randomread` results.
