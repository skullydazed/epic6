# PF loader

## Synopsis

```irc
/load -pf file [file ...]
/load -pf -args file arguments
```

The PF (“pre-formatted”) loader reads a script as one block of semicolon-separated statements. It removes full-line `#` comments and leading indentation, joins remaining physical lines with spaces, then executes the resulting block using normal [ircII syntax](ircii_syntax.md). It does not insert statement-separating semicolons for you.

`load` selects the standard loader initially; `-pf` selects PF for following files in that invocation, and `-std` switches back. Files are found through the normal [load](../commands/load.md) path and encoding handling. `-args` makes the following filename the last file in that invocation and passes its remaining text as the script's `$*`. Without it, additional words are filenames, not script arguments.

## Rule 0: Put a loader wrapper at the top

For a script intended to be loaded without arguments using either loader, the customary first line is:

```irc
if (word(2 $loadinfo()) != [pf]) { load -pf $word(1 $loadinfo()); return; };
```

`loadinfo()` reports the current line, filename, loader, and package; word `2` is the loader name. If the script entered through the standard loader, this reloads it using PF and returns from the original load. Once PF is active, the body continues normally.

This simple wrapper assumes a filename that can be represented as one ordinary word and does not forward script arguments. Scripts needing argument preservation or unusual filenames should arrange their loading explicitly rather than assuming this wrapper covers those cases. Direct `/load -pf file` needs no wrapper.

## Rule 1: Use supported comments

A `#` at the first non-whitespace position of a physical line makes the entire line a PF comment. The preprocessing is line-based, not a quoted-string-aware lexer; a physical line beginning that way is removed even inside a larger construct. A `#` after code on the same line is not an automatic end-of-line comment.

C-style `/* ... */` comments are not removed by PF. To retain such prose, put it in the argument of the no-op `:` command and terminate the statement:

```irc
: {
    /* Description of this script. */
};
```

Balanced braces still matter: the text is grouped as a normal argument, not handled by a separate multiline-comment grammar. The semicolon after `}` prevents following statements from becoming more arguments to `:`.

## Rule 2: Put semicolons between commands

Newlines are spacing, not statement boundaries:

```irc
assign first one;
assign second two;
```

Without the first semicolon, these become one `assign` statement with additional argument text. Blank lines and indentation do not fix a missing separator. A final semicolon is a useful convention, though the end of a block can end its last statement. A backslash at the physical end of a line is not a PF-specific continuation marker; ordinary escape handling still applies after lines are joined.

## Rule 3: Put braces around alias and hook bodies

PF executes command statements with normal expando substitution. Protect the body so `$` forms intended for later invocation are not expanded while loading:

```irc
alias greet {
    echo Hello, $0;
};
on ^join "*" {
    echo Joined: $*;
};
```

The outer alias or hook definition executes during loading. Its balanced braced body is preserved for that pass and evaluated when invoked. For hook pattern-expansion rules, see [on](../commands/on.md). This is the same protection mechanism used by ordinary script blocks, not an exemption from expansion for a particular command.

## Rule 4: Separate commands inside bodies too

Semicolons are needed at each nested level, not merely after the whole definition:

```irc
alias greet {
    local greeting Hello;
    echo $greeting, $0;
    echo Arguments: $*;
};
```

The semicolons inside the braces separate the body's commands; the one after the closing brace separates this definition from the next top-level statement. See [blocks](block.md) for grouping and semicolon protection.

## Execution and limitations

PF loads execute code, rather than merely validating or registering a file. Earlier definitions and side effects remain if a later statement fails. There is no general success return value or transactional rollback. Errors from file lookup, decoding, parsing, or executed commands retain their own behavior; nested loads are subject to the client's load-depth limit.

An executable file is accepted by this loader only when it starts with `#!`; that first line is skipped as a comment. Otherwise an executable file is rejected with `Cannot open ... -- executable file`. This does not mean the script is run by an external interpreter: PF still parses its contents as EPIC code.

The preprocessing does not maintain detailed physical line numbers while building the block, so diagnostics should not be assumed to identify the exact original line. No optional script package or server connection is required to use the loader; commands in a loaded script can have their own requirements and network effects.

## Source references

- `source/commands.c`: `load()`, `loader_pf()`, standard-loader comparison, load state, no-op comments, and block execution.
- `source/functions.c`: `loadinfo()` result layout.
- `source/expr.c`: block statement boundaries and expansion protection.
- `source/alias.c` and `source/hook.c`: alias definitions and hook-body registration.
