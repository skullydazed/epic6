# functions

## Synopsis

```text
/load functions
```

## Description

This script supplies display-formatting and list-manipulation functions written in EPIC's scripting language. Call them as `$name(arguments)`; arguments are normally separated by spaces, not commas, unless a particular helper explicitly uses commas in a selector. Their returned text can be used by other aliases or displayed with `echo`.

The script predates many current built-in functions. Loading it installs its aliases; it does not enable a separate C subsystem. Functions used underneath retain their normal parsing, word-quoting, numeric, and server-context rules. The helpers generally do not validate missing or malformed arguments or provide separate success flags.

## Formatting and compatibility

| Function | Behavior |
| --- | --- |
| `format(<width> <text>)` | Right-align short text to the requested width; return text unchanged when its tested length is at least that width. |
| `lformat(<width> <text>)` | Left-align short text, with the same no-truncation condition. |
| `pluck(<variable> <word>)` | Remove the first case-insensitive matching word from the named variable and return the modified list. This mutates the variable, rather than just filtering supplied text. |
| `notword(<index> <list>)` | Alias for [notw](../functions/notw.md), removing a word by its zero-based index. |
| `remove(<word> <list>)` | Alias for [remw](../functions/remw.md), removing the first matching word. |
| `min(<numbers...>)`, `max(<numbers...>)` | Numerically sort the input and return its first or last word. Empty input returns empty. |

`format` and `lformat` differ from ordinary `$[width]variable` expansion by testing the length before applying a potentially truncating field format. Their guard measures bytes, while the actual field formatter uses display width and the current padding character. They are therefore not reliable display-column padding helpers for every Unicode or control-code string. They also overwrite the global scratch variable `IRCII.word`. Prefer the current [pad](../functions/pad.md) built-in for new formatting code.

## Lists, matching, and ranges

| Function | Behavior |
| --- | --- |
| `regpattern(<expression> <list>)` | Keep words matching a case-insensitive extended regular expression. |
| `regfilter(<expression> <list>)` | Keep words not matching that expression. |
| `indextowword(<character-index> <text>)` | Replace double quotes with underscores, then call `indextoword`, preventing those quotes from grouping words. |
| `wwordtoindex(<word-index> <text>)` | Perform the same quote replacement before calling `wordtoindex`. |
| `jotm(<start> <end> [<start> <end>...])` | Expand endpoint pairs into inclusive numeric ranges using `jot`; ranges can ascend or descend. |
| `jotc(<endpoint-characters>)` | Convert endpoint bytes into ranges and return the corresponding bytes; use paired ASCII endpoints. |
| `maskm(<mask-type> <userhosts...>)` | Apply built-in `mask` to each supplied userhost. |
| `randn([<repetitions> [<limit>]])` | Return repeated `rand(limit)` results. A single integer specifies repetitions, not the limit; omitted repetitions default to one and the limit defaults to zero. |
| `replace(<template> <search> <replacements...>)` | For each replacement word, globally substitute the search text into a copy of the template and join the copies as a list. It uses `sar` syntax internally, so delimiter characters and complex quoting require care. |
| `shuffles(<list>)` | Build a shuffled word list by repeated random insertion. |
| `shufflef(<list>)` | Randomly split the list into two groups, then shuffle those groups; it recursively splits large inputs. |
| `shufflec(<text>)` | Shuffle the individual bytes using `ascii`, `shufflef`, and `chr`. This can break multibyte UTF-8 characters. |
| `uniqd(<list>)` | Drop the first word in each adjacent run of equal words, retaining subsequent repetitions. It does not sort input or return each duplicated value just once. |
| `wordn(<selector> <list>)` | Select zero-based words using comma-separated indices or dot-separated `start.end[.step]` ranges. |
| `cut(<selector> <separator> <text>)` | Select separator-delimited fields using the same comma/range notation, joining selected fields with the separator. |
| `words(<list>)` | Rebuild a list with single-space separators and quote processing through `unsplit`; it does not remove all spaces. |

Regular-expression helpers use `regcomp` and `regexec`, then attempt `regfree` cleanup. The current C `regfree` retains a length check for a different encoded representation and can reject the handle returned by `regcomp`, producing a diagnostic instead of freeing it. The aliases also do not check compilation errors before using the result. Supply a valid expression and quote it as one word when it contains spaces. The input is a list of words, not a multiline buffer. Word quoting and preservation in several helpers depend on the current `xdebug dword` or `extractw` behavior; do not assume arbitrary nested quoting survives unchanged.

`randn` does not seed or select a random-number generator. A positive limit gives results from zero through one less than that limit via the C `rand` function; zero uses its unrestricted random-value path. Repetition counts and range sizes are not bounded by these aliases, so avoid unintentionally large expansions.

## Variable and channel helpers

`splicec(<variable> <start> <count> <replacement>)` changes the named variable by converting its contents to byte values, calling `splice`, and converting back. It returns the removed bytes as a string and uses `bless` so the operation can reach a caller's local variable. Indices and counts are byte positions, not Unicode character positions; splitting a UTF-8 sequence can make invalid text.

`chvoices(<channel>)` and `chnovoices(<channel>)` return channel users with or without cached voice status. `chhops(<channel>)` and `nochhops(<channel>)` similarly select cached half-operators or their complement. They use the current server's locally known user list and mode flags; they do not query the server or independently verify current membership or privileges.

The retained `mychats()` alias calls the removed `dccctl` interface and does not enumerate working DCC chats in EPIC6. Loading this script does not restore the removed DCC subsystem.

## Examples

```text
/load functions
/echo $lformat(8 hello)
/echo $min(12 3 8)
/echo $jotc(afWZ)
/echo $replace(#x# x 0 1 2 3)
/echo $uniqd(a a a b c c)
```

These return `hello` padded on the right, `3`, `abcdefWXYZ`, `#0# #1# #2# #3#`, and `a a c`, respectively. Use `$wordn(0.4.2 a b c d e)` to select `a c e`.

## Other notes

The original collection was authored by Ian Frechette (Daemon); later additions include work by CrazyEddy. Many historical helpers were superseded by built-in capabilities, but the current file still defines the active aliases described here.

## Source references

- [script/functions](../../script/functions): helper definitions and generated aliases.
- [source/functions.c](../../source/functions.c): underlying list operations, byte conversion, numeric sorting, randomness, padding, regular expressions, and channel-mode queries.
- [source/expr.c](../../source/expr.c): byte-count expansion and field-width formatting.
- [source/if.c](../../source/if.c): `fe` mapping and list reconstruction.
- [source/names.c](../../source/names.c): cached channel users and status flags.
