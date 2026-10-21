# Standard loader

## Synopsis

```text
/load -std <file> [<file> ...]
```

The standard loader reads script text into an accumulator and executes completed statements as it goes. It is the default for the C built-in `load` command. Its line handling is an additional preprocessing layer, separate from normal [ircII syntax](ircii_syntax.md).

See [load](../commands/load.md) for file lookup and general options. `-std` selects this loader for subsequent files; `-pf` selects the [PF loader](pf_loader.md). The standard loader does not use the supplied `-args` text as its script argument string: its statement execution passes no argument context. Do not assume PF argument-passing behavior applies here.

## How lines become statements

The following describes the useful rules, rather than every interaction in the accumulator:

1. Read a physical line and skip its leading whitespace. Ignore an empty line or a line whose first non-whitespace character is `#`.
2. If the line ends in a single backslash immediately followed by newline, remove that pair and append the next physical line directly. The appended line retains its indentation, and an initial `#` there is data rather than a full-line comment. This repeats while the continuation conditions hold.
3. Scan the resulting logical line for braces, semicolons, and slashes, updating the accumulator and comment state.
4. Track unmatched opening braces so multiline bodies can be accumulated. At an ordinary new statement boundary inside an open body, insert a semicolon. Outside a body, execute the completed accumulated statement before starting the next one.
5. At end of file, execute any remaining complete statement; report an unmatched opening brace instead of executing that incomplete accumulator.

An opening brace at the beginning of the next logical line can attach to the preceding command rather than starting a separate statement. An explicit semicolon or a brace at a boundary can change whether another semicolon is inserted. The loader does **not** simply append a semicolon to every physical line.

Continuation requires the newline to be present and the backslash immediately before it. A backslash preceded by another backslash does not trigger continuation; this is a direct preceding-character check, not a general odd/even backslash-count rule. Continuation is also bounded by the loader's fixed line buffer. Avoid oversized physical or continued lines.

## Comments and punctuation

Full-line `#` comments are recognized after leading whitespace is skipped on an initial physical line. A `#` later in ordinary text is not a general inline comment marker.

C-style `/* ... */` comments are recognized by the standard loader. With [comment_hack](../set/comment_hack.md) enabled, an opening `/*` is recognized only at the beginning of the logical line after initial indentation removal; with it disabled, it can begin elsewhere. Comments are not nested. An unterminated comment is diagnosed at end of file.

The preprocessing scanner is not the expression parser: it tracks braces and scans punctuation without generally respecting quoted strings as opaque text. It skips punctuation immediately preceded by a backslash, again without counting a complete run of backslashes. Quoting a brace or slash in a string is therefore not a universal way to protect it from loader preprocessing. Unexpected closing braces produce a file/line diagnostic.

## Example

A simple standard-format file can omit statement semicolons at ordinary line boundaries:

```irc
# Standard-loader example
alias hello
{
    echo Hello from the standard loader
    echo Arguments: $*
}
```

Load it with `/load -std example.irc`, then invoke `/hello some text`. The braces let the loader accumulate the alias body; `$*` is expanded when the alias runs, not when the file defines it.

## Choosing a loader

For scripts whose statement boundaries should be explicit, use the [PF loader](pf_loader.md) and write the semicolons yourself. PF loads the script as one block and does not insert standard-loader semicolons. It still preprocesses physical lines, removes full-line comments and leading whitespace, and joins lines; it is not a byte-for-byte execution mode. Switching loaders can change a script's meaning, so select the format the script was written for.

Load errors are client diagnostics, not function return values. Execution is incremental: definitions and other effects from earlier completed statements are not rolled back when a later error occurs.

## Source references

- `source/commands.c`: `load`, `loader_std`, file reading, loader selection, accumulator state, comments, and diagnostics.
- `source/vars.c`: `comment_hack` setting registration.
