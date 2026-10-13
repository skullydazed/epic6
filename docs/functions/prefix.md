# prefix function

## Synopsis

`$prefix(<word list>)`

## Technical

Returns the common initial substring of all words in the list. Comparison is case-insensitive, using EPIC's Unicode uppercase mapping; the returned spelling comes from the first word. No input or no common initial substring returns the empty string. With one word, the whole word is returned, subject to result quoting described below.

Words use function word-list parsing. Double quotes group words containing spaces when [/xdebug dword](../commands/xdebug.md) is enabled. If the whole first word is shared by every word, the result is reconstructed as a word and quoted when it contains spaces with DWORD enabled. A shorter prefix is returned directly without adding grouping quotes. Original separators and quote spelling are not preserved.

The implementation tests increasingly long prefixes against the other words, repeatedly comparing from their beginnings. Large lists or long shared prefixes can therefore be expensive. It returns a new string without modifying variables supplied as input.

## Practical

Use this function for partial completion. If the input prefix is `foo` and the available words are `foobar` and `foobaz`, the common prefix is `fooba`; a completion script can insert that and show the remaining choices.

```text
@ users = pattern(foo* $onchannel())
@ result = prefix($users)
if (numwords($users) > 1) { beep }
```

[$pattern()](pattern.md) keeps matching cached channel nicknames from [$onchannel()](onchannel.md). If there are no matching names, `result` is empty; handle that before replacing input. [$numwords()](numwords.md) counts the candidates. This example computes a result and optionally beeps; input replacement belongs to the completion script.

## Returns

The common initial substring, using the first word's capitalization. There are no special numeric error returns or options.

## Restrictions

The mismatch path compares a count of Unicode code points but copies that count as bytes. Consequently a shared multibyte prefix may be shortened incorrectly or returned as invalid UTF-8. For example, `éa` and `éb` compare equal for their first code point, but the returned prefix contains only the first byte of `é`. Do not rely on this implementation for correct Unicode completion. ASCII inputs avoid this byte/code-point mismatch.

## Examples

| Expression | Result |
| --- | --- |
| `$prefix(foobar foobaz foobooya)` | `foob` |
| `$prefix(one two three)` | Empty string. |
| `$prefix(FOObar foobaz)` | `FOOba` |
| `$prefix(hello)` | `hello` |

## Source

Registration and `function_prefix()`: [source/functions.c](../../source/functions.c). `split_wordlist()`, Unicode case-insensitive comparison, and result quoting: [source/ircaux.c](../../source/ircaux.c).
