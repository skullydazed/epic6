# grep

## Synopsis

```text
/load grep
/grep [-w <window>] <regular-expression>
```

## Description

This script searches a window's stored lastlog text using a regular expression and displays matching entries. It demonstrates scripted lastlog scanning, similar in purpose to [lastlog](../commands/lastlog.md) with `-regex`, but with its own defaults and limitations. It does not search files or invoke the Unix `grep` program.

The default window in the current script is reference number `1`, not the selected input window. Supply `-w 0` to search the current window explicitly. The selector is passed through the `winlevel` compatibility wrapper, so a simple window name or other supported selector can also be used. If that wrapper returns an empty result, the script drops the selector and the underlying `line` calls fall back to the current window without an explicit error.

It requires `winlevel` from [builtins](builtins.md), normally available through default startup. Loading this script defines only `grep`, not `egrep`, despite an old usage comment in its source. The [files](files.md) script defines a different `grep` alias for local files; whichever definition is loaded last is the active one.

## Matching and output

All arguments after the optional `-w <window>` form the expression. The script calls `regcomp`, which uses case-insensitive extended regular expressions. Expressions can therefore contain spaces without being wrapped as a single quoted word; any quote characters retained by command parsing become part of the expression. There are no case-sensitive, inverse-match, context, or result-count options in this alias.

The scan starts at the current global [lastlog](../set/lastlog.md) setting and counts down to `1`, retrieving each entry through [line](../functions/line.md). Lastlog positions are one-based, with `1` the newest stored logical entry. Results are collected before display, then shown from older to newer within that scan range.

Output is bracketed by `Results of grep` and `End` headings. Each matching line is prefixed with its stored lastlog position in a four-column field. These are positions in the scanned window, not persistent message identifiers or physical terminal row numbers. The result copies use `xecho -nolog`; the headings use ordinary `echo`. Output goes through the current output context, not automatically into the window being searched.

With no argument, the alias prints `Usage: /grep [-w #] <text>`. An incomplete `-w` form returns silently. With no matches, only the result headings are printed. There is no function return value or success count defined.

## Limits

The loop uses the global lastlog setting rather than the selected window's actual size or individual retention limit. It can miss older entries when that window retains more records than the global setting. Positions beyond the window's stored entries return empty strings; an expression matching empty text can consequently produce numbered empty results for nonexistent positions.

Only retained lastlog entries can be searched. Changing retention or lastlog levels, clearing history, or deleting a window can remove searchable data; this helper does not retrieve it from the server or log files. Stored formatting bytes are part of the input to the regular expression.

The script does not check regex compilation errors before executing the compiled handle. Supply a valid expression; malformed expressions are not handled with a clean script-level diagnostic. Its final `regfree` call also encounters the current C implementation's stale encoded-handle length check, which can reject cleanup with a diagnostic instead of freeing the expression. This does not constitute a reliable validated regex-search interface.

## Examples

```text
/load grep
/grep -w 0 error|warning
/grep -w 3 connection failed
```

Search the current window for either word, or window `3` for the phrase. For built-in search options, use `lastlog -regex` instead.

## History

Written by wd (White_Dragon, Chip Norkus); first included in EPIC4pre2.510.

## Source references

- [script/grep](../../script/grep): window `1` default, global scan bound, result collection, and display.
- [source/lastlog.c](../../source/lastlog.c): `line` lookup, one-based positions, and out-of-range results.
- [source/functions.c](../../source/functions.c): case-insensitive extended `regcomp`, `regexec`, and the `regfree` length-check mismatch.
- [script/builtins](../../script/builtins): `winlevel` wrapper.
- [source/commands.c](../../source/commands.c): `xecho -nolog` output handling.
