# word

Extract one word from a list, counting from zero.

## Synopsis

```irc
$word(<index> <list>)
```

## Technical

The index selects a zero-based word position: `0` is the first word, `1` the second, and so on. Missing arguments, a negative index, or an index at or beyond the number of words return an empty string.

List parsing follows `/xdebug dword`. With it disabled, words are separated by whitespace and quotation marks are ordinary text. With it enabled, a double-quoted phrase is one word; the function removes its grouping quotes and unescapes quoted characters according to the word parser. The result is plain extracted text and is not re-quoted even if it contains spaces. An empty quoted word consequently returns an empty string without indicating an error.

The index uses permissive base-zero integer conversion, then is stored in a C `int`: numeric prefixes can be accepted, nonnumeric text becomes zero, and very large values can be narrowed. Use an ordinary nonnegative decimal index for predictable selection. This function does not change a variable or the original list.

## Practical

Use this function when you need one particular word. Use [qword](qword.md) when the extracted value must be quoted for reinsertion into a word list; both functions use the same `/xdebug dword` policy for recognizing input words.

## Returns

The selected word without grouping quotes, or an empty string for a missing word or an empty selected word.

## Examples

```irc
$word(2 foo bar blah)  /* blah */
$word(4 foo bar blah)  /* Empty. */
$word(-1 foo bar blah) /* Empty. */
```

With `/xdebug -dword`, `$word(1 one "two three" four five)` returns the literal text `"two`. With `/xdebug dword`, the same call returns `two three`, without quotes.

## History

This function already existed in ircII.

## Source

Registered and implemented as `function_word` in [source/functions.c](../../source/functions.c). `next_func_arg` is defined in [include/words.h](../../include/words.h), using the word parser in [source/ircaux.c](../../source/ircaux.c).
