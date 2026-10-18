# set comment_hack

## Synopsis

```text
/set comment_hack [on|off|toggle]
```

Default: `on`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

Controls where the standard legacy script loader recognizes C-style `/* ... */` comments:

- `on`: a comment can start only at the beginning of a loaded line, after leading whitespace has been skipped.
- `off`: the loader can recognize a comment opening later in a line as well.

This compatibility behavior lets older scripts use slash-star text in inline ASCII graphics without having it mistaken for a comment. It controls recognition of the opening delimiter, not a different closing delimiter: once inside a comment, the loader looks for `*/`. An unterminated comment produces a loader diagnostic at the end of the file.

The standard loader has its own delimiter-scanning and escape rules; do not assume these are a complete C lexer or that quotes automatically protect every inline `/*`. With the setting off, literal slash-star text later in a line can be consumed as a comment.

## Loader scope

The setting is consulted by [load](../commands/load.md)'s standard loader (`load -std`). The preformatted loader (`load -pf`) uses a separate parser and does not consult it; that loader strips lines beginning with `#` after leading whitespace rather than using this C-style comment switch. The ordinary [comment](../commands/comment.md) command is also separate.

Changing this setting affects subsequent processing by the standard loader; it does not reprocess definitions already loaded and sends no IRC request. It has no immediate callback.

## Example

Allow inline C-style comments while loading a legacy script:

```text
/set comment_hack off
/load -std legacy-script
/set comment_hack on
```

Use the appropriate value for the script's syntax, especially if it contains literal slash-star graphics.

## History

This setting replaced the compile-time `COMMENT_HACK` definition used before EPIC3.001.

## Source references

- [source/commands.c](../../source/commands.c): `loader_std` checks the setting against the first non-whitespace position; `loader_pf` has independent line-comment processing.
- [source/vars.c](../../source/vars.c): boolean registration and standard value handling.
- [include/config.h](../../include/config.h): default on.
