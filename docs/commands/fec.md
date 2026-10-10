# fec

Loop through a string one character at a time, or rewrite it using the values produced by a loop body.

## Synopsis

```text
/fec (string) variable [variable ...] { commands }
/fec source_variable variable [variable ...] { commands }
```

## Description

`fec` works like [fe](fe.md), except it consumes Unicode code points from UTF-8 text instead of whitespace-delimited words. Each loop variable receives one code point, and the body runs after all variables in the group have been assigned. Multiple variables therefore process groups of characters. This is not iteration by displayed character: combining marks and the components of an emoji sequence can occupy separate iterations.

The parenthesized form expands its string once before iteration. Literal leading and trailing whitespace inside the parentheses is trimmed before that expansion; interior whitespace is processed as characters. Whitespace introduced by a variable expansion is not subject to that initial trimming. Quotes in the expanded string are ordinary characters, not word-grouping syntax.

Loop variables are [local](local.md), not global. Existing visible locals are updated, or new locals are created in the current scope. They remain visible after the command finishes in that scope, and their previous values are not restored. The body receives the enclosing argument context; use the loop variables to access characters.

In the second form, give the source variable's name without `$`. Its initial value supplies the string, including leading and trailing whitespace. After each body execution, the loop variables' current values are concatenated without separators. After the loop, that result replaces the source variable using ordinary [assign](assign.md) scope rules. Values may contain more than one character or be empty, so the body can replace, expand, or remove characters.

An empty string executes no iterations and leaves a source variable unchanged. In a final incomplete group, the remaining loop variables receive empty strings. If the decoder encounters an invalid UTF-8 byte, that byte is skipped and the corresponding variable is not updated; its previous value can consequently reach the body and the rewritten result. Use valid UTF-8 input rather than relying on malformed-byte handling.

[break](break.md) stops after the current values have been appended in the rewriting form, discarding the unprocessed suffix. [continue](continue.md) also appends those values before proceeding to the next group. A propagated return or other control-flow exception stops the loop after the same append step.

## Examples

Display each code point:

```text
fec (hello) character {
    echo $character
}
```

Rewrite a variable by removing hyphens:

```text
@ text = [a-b-c]
fec text character {
    if (character == [-]) {
        @ character = []
    }
}
echo $text
```

The resulting string is `abc`.

## Restrictions

Supply at least one valid variable name and a brace-delimited body, with whitespace before its opening brace. An empty body is allowed. At most 255 loop variables are accepted. Missing strings, command blocks, closing braces, and excessive variable counts produce diagnostics. There are no command options or special build requirements; the built-in is always registered.

## History and source

`fe` and `fec` have used local loop variables since January 22, 1997.

Registration is in [source/commands.c](../../source/commands.c). The shared handler's character branch, parenthesis parsing, mapping, and control flow are in [source/if.c](../../source/if.c). UTF-8 decoding is in [source/wcwidth.c](../../source/wcwidth.c), encoding and result concatenation are in [source/ircaux.c](../../source/ircaux.c), and local and source-variable assignment are in [source/alias.c](../../source/alias.c).
