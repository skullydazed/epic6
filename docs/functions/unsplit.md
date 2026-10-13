# unsplit

Join a list of words with a separator.

## Synopsis

```irc
$unsplit(<separator> <wordlist>)
```

## Technical

Both the separator and the word list always honor double-quoted words, regardless of `/xdebug dword` or `/xdebug extractw`. Quote the separator to use a space or a longer string containing spaces. The entire separator is used, rather than just its first character.

Grouping quotes are removed from the input words. The result joins the nonempty words in their original order, with one separator between each pair. It does not add grouping quotes to the result. Empty quoted words are omitted, and there is no leading or trailing separator. An empty separator concatenates the words directly.

## Practical

Use this function to present a word list in a particular format, including lists produced by [split](split.md). Joining with a space can turn a list of quoted words into ordinary space-separated text, but loses the original grouping: a quoted phrase becomes several ordinary words in the result.

## Returns

The joined text. Missing arguments, a separator without any list words, or a list containing only empty words return an empty string. This function does not modify a variable.

## Examples

```irc
$unsplit(: a b c)                /* a:b:c */
$unsplit(" " "hello there" world) /* hello there world */
$unsplit(" / " a b c)            /* a / b / c */
$unsplit(: a "" b)               /* a:b */
$unsplit("" a b c)               /* abc */
```

## Source

Registered and implemented in [source/functions.c](../../source/functions.c) as `function_unsplit`. Quoted-word parsing and joining are implemented by `universal_next_arg_count`, `malloc_strcat_word`, and `malloc_strcat_wordlist` in [source/ircaux.c](../../source/ircaux.c), with the `new_next_arg` macro in [include/words.h](../../include/words.h).
