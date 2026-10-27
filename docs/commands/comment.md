# comment command

## Synopsis

```irc
/comment [<anything>]
/: [<anything>]
```

## Description

The built-in `comment` command does nothing with its arguments. The colon (`:`) is another name for the same command; command names need not consist of letters. `#` is also registered to this no-op handler when it reaches command dispatch, although full-line `#` comments in loaded files are normally removed earlier.

A command comment follows ordinary [ircII syntax](../special/ircii_syntax.md). Put multiline text in balanced braces:

```irc
comment {
    This is a multiline comment.
    Its text is an argument, not code to execute.
};

: {
    This is another multiline comment.
};
```

The semicolons make statement boundaries explicit, including for the [PF loader](../special/pf_loader.md). Braces still have to balance: this is a normal command argument, not a separate comment grammar that ignores all punctuation.

The no-op handler does not evaluate or execute its arguments. Normal command parsing happens before dispatch, however, so unprotected variable and function expressions can be expanded in a script context before `comment` runs. A command comment is not a general way to make arbitrary script text inert.

## Command comments and loader comments

Both [`load`](load.md) loaders remove physical lines whose first non-whitespace character is `#`, subject to their own line-handling rules. These full-line comments do not become part of a stored alias or hook body and incur no command execution when that body later runs. Each `#` comment ends at the physical newline; commenting out several lines requires a `#` on each line. An inline `#` after other code is not a general end-of-line comment marker.

By contrast, `comment` and `:` remain ordinary command text. When retained inside an alias or hook, they are parsed whenever execution reaches them. Prefer full-line `#` comments for ordinary notes inside frequently executed bodies or for disabling lines of real code; command comments are useful when a balanced block of prose is convenient.

The [standard loader](../special/standard_loader.md) also recognizes C-style `/* ... */` comments. The [`comment_hack`](../set/comment_hack.md) setting controls whether an opening delimiter is recognized only at the beginning of the logical line or elsewhere. These comments do not nest. PF does not remove C-style comments. For new scripts, use full-line `#` comments or an explicitly delimited command comment appropriate to the chosen loader.

## Source references

- [source/commands.c](../../source/commands.c): command registration maps `COMMENT`, `:`, and `#` to `commentcmd()`; `parse_statement()` performs normal command expansion and dispatch; `loader_std()` and `loader_pf()` implement loader-specific comments.
- [source/vars.c](../../source/vars.c): `COMMENT_HACK` registration.
