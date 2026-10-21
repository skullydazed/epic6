# What is a word?

EPIC supports several word-extraction policies. A function receives an argument string and then parses it according to its own rules; double quotes do not universally turn a phrase into one argument.

| Name | Meaning |
| --- | --- |
| `uword` | Unquoted word: quotes have no grouping effect, and whitespace separates words. |
| `qword` | Quoted-word grouping is recognized, with the surrounding quotes retained in the returned text. |
| `dword` | Quoted-word grouping is recognized, with the surrounding quotes removed when extracted. |
| `word` | Common function-argument policy: unquoted words with `xdebug dword` off, double-quoted words with it on. |
| `eword` | Extraction policy controlled separately by `xdebug extractw`. |

These names describe parsing policies, not distinct kinds of stored variables. A string has no permanent word type.

## Whitespace and quoted words

Ordinary word boundaries use byte-oriented `isspace(3)` tests. Spaces, tabs, and the other ASCII whitespace characters separate unquoted words; locale can affect additional bytes. This is not Unicode whitespace classification. See [space characters](space_character.md).

For ordinary double-quoted-word recognition, the opening quote must be at the start of the string or immediately after whitespace. Its matching closing quote must be followed by whitespace or the end of the string. Backslash protects the next byte while searching for that closing quote. Quotes embedded in an otherwise unquoted word do not provide general shell-style grouping or concatenation.

For example, given this argument text:

```text
one "two three" four
```

Unquoted parsing sees four words: `one`, `"two`, `three"`, and `four`. Quote-aware parsing sees three: `one`, `"two three"`, and `four`. Dequoting the middle word yields `two three`. The empty quoted string `""` can represent one empty word when quote-aware parsing is enabled.

An unterminated quoted word can consume the remaining string instead of producing a syntax error. Do not rely on malformed quoting to provide consistent boundaries across different functions.

## The two xdebug policies

`/xdebug dword` enables double-quoted function arguments for helpers that use the ordinary function-word policy; `/xdebug -dword` disables it. `/xdebug extractw` and `/xdebug -extractw` independently control helpers using the extraction-word policy. Both flags are clear in the initial C debug mask, but startup scripts or user commands can change them.

Some callers explicitly require quoted words, while others explicitly prohibit grouping or use custom delimiters. Those choices override any expectation that these flags affect every command or function. Consult the particular feature's syntax before changing a global parsing flag to accommodate one call.

## Why it matters

A quoted phrase can change the number of arguments, the indices used for selection, and whether returned text still includes quote characters. Counting and extraction must use compatible policies if their indices are to agree. A function's numeric arguments can also use a different parser from its later text arguments.

Word extraction is separate from [expando substitution](expando_substitution.md), [expression](expressions.md) string syntax, and a shell's quoting rules. A quote may protect text in one layer and be ordinary data in another. Check both evaluation timing and the receiving command's argument rules when passing strings through several layers.

## Source references

- `include/words.h`: unquoted, always-quoted, function-word, and extraction-word helper policies.
- `source/words.c`: whitespace boundaries, quoted-word search, and debug-flag selection.
- `source/ircaux.c`: shared argument extraction and dequoting.
- `source/debug.c`: initial mask and `dword`/`extractw` controls.
- `source/functions.c`: individual function callers select their parsing helpers.
