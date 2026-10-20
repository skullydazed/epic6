# less

## Synopsis

```text
/load less
/less -help
/less <filename>
```

## Description

This script defines a simple file pager which displays text a page at a time in the window from which it was invoked. Between pages it prompts `Enter q to quit, or anything else to continue`. It does not run the Unix `less` program and has no search, backward-paging, or command-line option interface beyond `-help`.

With no argument, or with `-help`, it prints `Usage: /less <filename>`. Otherwise it checks whether the path is readable and opens it for reading. A path failing that check produces `<filename>: no such file.`, even when the actual problem is permissions rather than absence. The script does not separately check whether the following open succeeded.

It uses `winnum` and `winsize` from [builtins](builtins.md), normally supplied by default startup; load that support first if your configuration omits it. The command does not modify the viewed file or send IRC requests and defines no function return value.

## Paging

The initial page budget is the invoking window's display height minus one. The script estimates how many screen rows each file line occupies through `numlines`, rather than simply counting file lines. It can display a complete long logical line even when that takes it past the remaining page budget.

Each output line is sent with `xecho -w` to the saved window refnum. Switching input windows therefore does not make the text follow you. The page budget remains the initially saved count, however, while width is recalculated from the current window's geometry on each page. Switching between differently sized windows or resizing can make the estimate inaccurate. Renumbering or deleting the original window also invalidates the assumption that its saved numeric refnum still identifies the same destination.

Press `q` to stop, or another character to request another page. The script is not re-entrant: it stores the active descriptor, page budget, and destination in shared variables `less.fd`, `less.nl`, and `less.w`. Starting another view while one is waiting overwrites that state and can resume the wrong file or leave resources open.

The prompt is a character-input callback, not a line to send to IRC. The script does not implement a safe independent viewer per window or per server. Very small windows whose page budget is zero or negative can repeatedly prompt without reading any data.

## Formatting and file limits

Before each page, the script saves `indent` and the obsolete name `continue_line`, turns indentation off, and attempts to unset `continue_line`; afterward it pops the saved settings. EPIC6's actual continuation-prefix setting is [continued_line](../set/continued_line.md), so those old `continue_line` operations are not a working way to suppress or restore that prefix and can produce setting/stack diagnostics. The `indent` setting is still restored through its stack entry.

Reads use EPIC's text-file functions: newline terminators are removed and invalid UTF-8 can be recoded using the `scripts` encoding rule. This is not a byte-preserving binary viewer. The script checks EOF after reading but before printing, so a final line without a newline terminator can be omitted. Filenames containing spaces are unreliable because the underlying file-function calls do not quote the substituted path.

At ordinary EOF it closes the local file handle. The `q` callback instead calls `close($file)`, referring to the page helper's local argument after that helper has returned; it does not use the saved `less.fd`. Thus quitting does not reliably close the open descriptor. The shared variables are not cleared as a verified cleanup step, and file errors are not reported through a structured status.

## Example

```text
/load less
/less notes.txt
```

Read forward using the prompt, or press `q` to stop displaying pages, accounting for the descriptor-cleanup limitation above. For independent simultaneous viewers or full pager controls, this script is not an equivalent to an external pager.

## History

Written for EPIC3 by hop in 1996, inspired by archon's [more](more.md) script. Later revisions added argument lists, wrapped-line counting, and temporary formatting changes.

## Source references

- [script/less](../../script/less): page loop, saved destination, prompt callback, and cleanup.
- [source/functions.c](../../source/functions.c): `fexist`, geometry, `numlines`, and file-function arguments.
- [source/files.c](../../source/files.c): line reading, EOF handling, recoding, and descriptor close.
- [source/commands.c](../../source/commands.c) and [source/screen.c](../../source/screen.c): character-input callbacks and `xecho` window selection.
- [source/vars.c](../../source/vars.c): current `indent` and `continued_line` setting names.
