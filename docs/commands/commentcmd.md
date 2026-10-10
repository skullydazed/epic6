# comment, #, and : commands

The `comment`, `#`, and `:` commands do nothing and ignore their arguments. Use them to put a note in a command sequence without producing output or sending anything to an IRC server.

## Synopsis

```text
/comment [text]
/# [text]
/: [text]
```

`text` is optional and has no meaning to the built-in command. There are no options or required arguments. Put a space between the command name and the text: `/#note` and `/:note` name different commands.

## Technical

The built-in handler does nothing, including when no text is supplied. It produces no output and reports no argument errors. All three names use the same handler and have the same built-in behavior. All three commands are available in every build and need no server connection.

Normal command processing happens before the handler runs. In contexts that perform `$` expansion, variables and functions in the text can be evaluated, including functions with side effects. In a block of commands, a semicolon can end the comment statement and allow the following command to run. Therefore, this command does not protect arbitrary text from the script parser.

A script alias with the same name can override any of these built-in commands. At the input prompt, `//comment text`, `//# text`, or `//: text` bypasses such an alias and selects the corresponding built-in handler.

## Practical

When typing interactively, include the leading `/`. A line typed without `/` is treated as text for the current conversation, not as a command.

The [`load`](load.md) command's standard and preformatted loaders separately discard file lines whose first non-whitespace character is `#`. Such lines are file comments, removed before command execution; they do not invoke this handler. A line beginning with `comment` or `:` receives ordinary command processing instead. For ordinary notes on their own lines in a script file, use the `#` file-comment form.

A `comment` or `:` command retained in an alias is parsed each time it runs. File comments are removed during loading and incur no such runtime parsing. The standard loader also recognizes C-style `/* ... */` comments for compatibility; these are loader syntax, not behavior of the built-in handler.

## Examples

At the input prompt:

```text
/comment This is a local note
/# This is a local note
/#
/: Another local note
/:
```

In a script file:

```text
# This entire line is a file comment
```

For a multiline note in a script, surround its text with braces so the statement parser treats it as one command argument:

```text
comment {
    This is a multi-line comment
    This text is ignored by the handler
}
: {
    This is another multi-line comment
}
```

Keep the braces balanced and follow normal script syntax. These forms do not bypass parsing or provide a safe container for arbitrary malformed code.

In a command block, this note ends at the semicolon, and `echo` still runs:

```text
{ # A note; echo This command runs }
```

## Source

See [source/commands.c](../../source/commands.c): the `#`, `:`, and `comment` registrations in `irc_command`, the empty `commentcmd()` handler, `parse_statement()` for command expansion and alias dispatch, `parse_block()` for statement separation, and `loader_std()` and `loader_pf()` for file comments.
