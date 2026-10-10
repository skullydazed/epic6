# lastlog

Search retained window output and redisplay matching logical lines, or append them to a file.

## Synopsis

```text
/lastlog [options] [pattern] [number [skip]]
```

## Description

EPIC retains output in a lastlog associated with each window. Its capacity is controlled by `/set lastlog` or `/window lastlog`, and its retained output levels by `/set lastlog_level` or `/window lastlog_level`. Only entries marked for lastlog retention are searched; the command cannot recover discarded history or output excluded from retention.

With no filters, `lastlog` redisplays retained entries for the current window. The default order is oldest to newest. `-reverse` searches and displays newest first. Output appears in the current window even when another window's history is selected. The current window's lastlog recording mask is temporarily cleared while the command runs, preventing ordinary redisplayed output from becoming new searchable entries.

A bare nonnumeric argument supplies the text pattern; a later bare text argument replaces it. The first bare number supplies the candidate span, and the next supplies the skip count. Use `-literal` for a numeric text pattern. Double quotes group option arguments containing spaces. Options can be interspersed with other arguments and are case-insensitive; abbreviations use the first matching option, so full names avoid ambiguity.

## Options

| Option | Behavior |
| --- | --- |
| `-` | Suppress the `Lastlog:` header and `End of Lastlog` footer. |
| `-literal pattern` | Require a wildcard match against `*pattern*`; despite its name, wildcard characters remain active. |
| `-ignore pattern` | Exclude lines matching `*pattern*`. |
| `-regex expression` | Require a case-insensitive POSIX extended regular-expression match. |
| `-regignore expression` | Exclude matching regular-expression lines. |
| `-target pattern` | Match the entry's recorded target, such as a channel or nickname, using wildcard matching. |
| `-maximum number` | Limit matching output entries; `-max` is accepted. Zero produces no output. |
| `-number number` | Limit the search span to the most recent qualifying entries before text and target filtering. |
| `-skip number` | Skip leading retained entries in the selected traversal direction. |
| `-reverse` | Traverse and display newest to oldest. |
| `-context before[,after]` | Include surrounding entries; one number supplies both sides. Negative values are treated as zero. |
| `-separator text` | Set the separator between nonoverlapping context groups; default `----`. |
| `-mangle types` | Normalize text using the specified mangle types before text matching and output. |
| `-rewrite text` | Expand this template for each displayed entry. |
| `-window window` | Search another window, selected by name or reference. |
| `-this_server` | Search retained entries in all windows on the selected window's server. |
| `-global` | Search all windows, overriding the server restriction. |
| `-file filename` | Append output to an existing file instead of displaying it. |
| `-level` | Add an output level to the selection mask, for example `-public` or `-msg`. |
| `--level` | Remove a level from the selection mask. |
| `-all`, `--all` | Set or clear the selection mask. An empty mask means unrestricted levels, not no output. |

Text, regular-expression, target, and level filters combine. To exclude a level, begin with `-all` and then remove it, for example `-all --msg`. Context entries can bypass these match filters, but remain subject to window selection and retention visibility. Before and after refer to traversal order, so their chronological meanings reverse with `-reverse`. Context output and match limits interact; use `-maximum` primarily without context when you need a simple output count.

`-number` chooses a recent span by retained entries and selected levels; it is not a starting position. `-skip` then skips entries from the beginning of the traversal, before level or text matching. The skip-count precheck uses the invocation window's retained size even with broader window selections. Zero-number edge cases do not reliably mean an empty search unless an explicit nonnegative skip is also supplied; use `-maximum 0` when you need no output.

Regular expressions are compiled with `REG_EXTENDED`, `REG_ICASE`, and `REG_NOSUB`; they do not supply captured groups to rewriting. Invalid expressions, unknown flags, invalid windows, and missing or invalid required numeric arguments produce diagnostics and stop the command.

## Rewriting and files

The default rewrite template comes from `/set lastlog_rewrite`; `-rewrite` overrides it for this invocation. Its argument context is:

| Argument | Value |
| --- | --- |
| `$0` | Unique lastlog entry reference |
| `$1` | Creation timestamp, suitable for `strftime()` |
| `$2` | Window user reference number |
| `$3` | Numeric output level |
| `$4`, `$5`, `$6` | Reserved placeholders (`.`) |
| `$7` | Recorded target, or `.` when absent |
| `$8-` | Matched or mangled line text |

The metadata-and-text argument string is limited to 10,240 bytes. Account for ordinary surrounding script expansion when supplying a template intended to expand later.

File names support tilde expansion and are resolved with `realpath`, so the destination must already exist. Output is appended, never truncated. Invalid paths or failed opens produce diagnostics. Header, footer, separators, and rewritten lines also go to the file unless suppressed. File output has no separate destination-encoding conversion.

## Examples

```text
/lastlog
/lastlog "help me"
/lastlog -target #epic -public
/lastlog -reverse -max 5 -target #epic "help me"
/lastlog -literal 1234
/lastlog -regex "hi.*there"
/lastlog -all --msg
/lastlog -file lastlog.out foo
```

The final example requires an existing `lastlog.out` file and appends lines containing the pattern `foo`. A target filter searches recorded metadata rather than merely finding the target name in the displayed text.

## History and source

`lastlog` first appeared in ircII. `-rewrite` first appeared in EPIC5-0.3.5. The built-in is always registered.

Registration is in [source/commands.c](../../source/commands.c). Retention, option parsing, selection, context, and rewriting are in [source/lastlog.c](../../source/lastlog.c). Settings are registered in [source/vars.c](../../source/vars.c), level names are handled in [source/levels.c](../../source/levels.c), path normalization is in [source/ircaux.c](../../source/ircaux.c), and file-versus-window output is in [source/output.c](../../source/output.c).
