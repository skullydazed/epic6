# Fe

For word-list iteration, see the [fe command](../commands/fe.md):

```text
/fe (list) variable [variable ...] { commands }
/fe source_variable variable [variable ...] { commands }
```

The parenthesized form expands the list once, then assigns successive groups of words to local loop variables. Multiple variables process several items per iteration. Quoted-word grouping depends on `xdebug dword`; see [what is a word?](../special/what_is_a_word.md).

The source-variable form reads that variable's initial list and replaces it with the loop variables' resulting values after processing. For example, in a script:

```irc
fe list item {
    @ item = item * item;
}
```

This squares the numeric words in `list`. It rewrites the source list, omitting empty values and not preserving its original spacing or quoting. [break](../commands/break.md) still includes the current group's resulting values before discarding the unprocessed remainder. The command page covers scope, control flow, and errors.

For character iteration instead, see [fec](../commands/fec.md). It consumes UTF-8 code points rather than words; a code point is not necessarily a whole displayed character. These loops execute local script code, and only their bodies determine any output or server requests.

## Source references

- `source/commands.c`: `fe` and `fec` command registration.
- `source/if.c`: shared implementation with separate word/code-point branches and list rewriting.
- `source/words.c`: word extraction policies.
