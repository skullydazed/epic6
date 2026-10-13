# $splice()

## Synopsis

`$splice(<variable name> <index> <count> [text])`

## Technical

Removes up to `count` words starting at zero-based word `index` from the named variable, inserts the replacement text at that point, writes the new value back, and returns the removed text. Omitted replacement text deletes the selected words. A count of zero inserts text without removing words.

If the index is at or beyond the word count, replacement text is appended and nothing is returned. If the removal extends past the end, all remaining words are removed. No empty slots are added for an index beyond the end.

A negative index **does not count from the end**. Instead, it describes a removal beginning before word zero: the implementation adds the negative index to the count and sets the index to zero. If that adjusted count is zero or negative, it returns empty without assignment. For example, index `-1` with count `3` removes the first two words; index `-1` with count `1` does nothing.

The variable name uses normal function word parsing. Index and count use permissive integer conversion, accepting numeric prefixes and base prefixes; nonnumeric text becomes zero. The entire remaining text is the replacement, including spaces and literal quotation marks. Missing required arguments return empty before writeback.

Word boundaries honor [/xdebug dword](../commands/xdebug.md). Extracted parts retain their internal spacing and quotation marks rather than being dequoted and reconstructed word by word. Ordinary surrounding whitespace is trimmed by extraction, and the retained prefix, replacement text, and retained suffix are joined with one separator between nonempty parts. Replacement text is joined as a word list, not quoted as one word.

## Practical

Use this to replace or remove several words in a variable without manually rebuilding the whole list. [$findw()](findw.md) can locate a literal word before splicing. Supply the variable's name, not its expanded value. An existing visible local variable is updated; otherwise writeback is global. `:name` and `::name` explicitly select local or global scope. See [/local](../commands/local.md) and [/assign](../commands/assign.md).

Empty global writeback removes the assigned value; a local variable remains defined with an empty value. An undefined variable acts as an empty list and can receive appended replacement text.

## Returns

The removed words, not the new value. Empty can mean an insertion, an out-of-range append, a missing argument, or a clipped negative-index interval that makes no change. There are no options or IRC connection requirements.

## Restrictions

Use a nonnegative count. Negative counts are not rejected consistently and can duplicate retained words rather than describing a meaningful removal. Very large indexes or counts can overflow C integer arithmetic; use values appropriate to the actual list size. Assignment errors can print diagnostics and are not represented by a separate function result.

## Examples

Starting with `assign foo one two three four five`, in this order:

| Expression | Returned text | New value of `foo` |
| --- | --- | --- |
| `$splice(foo 1 3 foo bar blah)` | `two three four` | `one foo bar blah five` |
| `$splice(foo 0 2)` | `one foo` | `bar blah five` |
| `$splice(foo 2 1)` | `five` | `bar blah` |

Starting with `assign sample one two three`, `$splice(sample -1 3 x)` returns `one two` and leaves `x three`. `$splice(sample 1 0 y)` then returns empty and leaves `x y three`.

Registration and `function_splice()`: [source/functions.c](../../source/functions.c). Word counting and extraction: [source/words.c](../../source/words.c). Joining: [source/ircaux.c](../../source/ircaux.c). Scoped writeback: [source/alias.c](../../source/alias.c).
