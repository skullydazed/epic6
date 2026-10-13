# $numwords()

## Synopsis

`$numwords(<word list>)`

## Technical

Counts words in the entire supplied text. Ordinarily, whitespace separates words; punctuation and Unicode characters within a word do not add to the count. With [/xdebug dword](../commands/xdebug.md) enabled, a double-quoted group beginning at a word boundary counts as one word, even if it contains spaces. An empty quoted word `""` also counts as one. With it disabled, whitespace separates words even inside quotes.

A quoted group ends at an unescaped closing quote followed by whitespace or the end of input. If no such closing quote exists, the group extends to the end. This is word-list parsing, not shell parsing: single quotes do not group words.

## Practical

Use this instead of the `$#var` notation when counting literal text, a parameter expando, or a function result without first assigning it to a variable. The two forms can differ on quoted groups: this function uses `xdebug dword`, while the `$#` expansion uses `xdebug extractw`.

[$strlen()](strlen.md) counts Unicode code points rather than words, and [$word()](word.md) selects a word by position.

## Returns

An integer count. Empty input returns `0`. The function does not change the supplied text or source variable and has no separate error result for unmatched quotes.

## Restrictions

The current counter has a whitespace edge case: a nonempty remainder containing only whitespace still counts as one word. Thus whitespace-only input returns `1`, and two or more trailing whitespace bytes after an ordinary word can add one extra count. Leading and repeated internal separators otherwise do not create extra words. Remove trailing whitespace first when that edge case would affect your use.

## Examples

| Expression | Result |
| --- | --- |
| `$numwords(hello there bob)` | `3` |
| `$numwords()` | `0` |
| `$numwords(hello   there bob)` | `3` |
| `$numwords("two words" three)` | `2` with `xdebug dword` enabled; `3` with it disabled |
| `$numwords("")` | `1` |

Registration and `function_numwords()`: [source/functions.c](../../source/functions.c). Counting, quoted groups, and whitespace movement: [source/words.c](../../source/words.c), `count_words()` and `move_to_next_word()`. `$#` expansion: [source/expr.c](../../source/expr.c).
