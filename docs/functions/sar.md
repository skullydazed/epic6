# $sar()

## Synopsis

`$sar([c][i][g][r]/<search>/<replace>/<text>)`

## Technical

Returns the text with its first occurrence of the search string replaced by the replacement. The default search is case-insensitive. Search and replacement are literal strings, not wildcard patterns or regular expressions; replacement text has no backreference syntax.

Arguments consist of zero or more lowercase option letters, an opening delimiter, the search string, the same delimiter, the replacement, the same delimiter, and the final text. The delimiter can be any valid Unicode codepoint other than the option letters `c`, `i`, `g`, and `r`; `/` is customary. There is no delimiter escaping. Search and replacement cannot contain the delimiter, but final text can. Spaces are literal content and quotation marks do not group or dequote segments.

| Option | Effect |
| --- | --- |
| `c` | Case-sensitive search |
| `i` | Case-insensitive search, the default |
| `g` | Replace successive nonoverlapping occurrences, up to 99,999 |
| `r` | Treat the final text as a variable name, read its value, and assign the result back to that variable |

Options can be repeated or reordered. When both `c` and `i` appear, the last controls case sensitivity. Uppercase option letters are treated as delimiters instead.

Replacement scans the original text once and skips matched search text. Inserted replacement text is not searched again, even with `g`; a replacement containing the search string does not cause repeated expansion.

## Practical

Use this general-purpose search-and-replace function to substitute arbitrary substrings. Normal EPIC expression expansion can supply the search, replacement, and text. With `r`, provide the variable name without `$`; the result is both returned and assigned using normal [/assign](../commands/assign.md) scope rules. Without `r`, a source variable's value is unchanged. [$msar()](msar.md) applies several search/replacement pairs in order.

## Returns

The resulting string. No match returns the original text. An empty replacement deletes matched text.

Missing input, options without a delimiter, an empty search, missing delimiters, or empty final text returns the empty string without substitution or variable assignment. In particular, an empty search does not return the supplied text unchanged. Empty resulting text can also be a successful substitution. With `r`, an undefined or empty variable produces an empty result through the normal assignment path.

## Restrictions

Case-sensitive searches compare bytes. Case-insensitive searches compare Unicode codepoints using EPIC's uppercase mappings, but scanning and removal still advance by bytes and by the search string's original byte length. Use care with malformed UTF-8 or case-equivalent strings with different byte lengths; this is not Unicode-normalized replacement.

## Examples

| Expression | Result |
| --- | --- |
| `$sar(/oo/ee/booyamon)` | `beeyamon` |
| `$sar(/oo/ee/foofoo)` | `feefoo` |
| `$sar(g/oo/ee/foofoo)` | `feefee` |
| `$sar(g/a/aa/aaa)` | `aaaaaa` |
| `$sar(/a//banana)` | `bnana` |
| `$sar(c/FOO/bar/foo)` | `foo` |
| `$sar(//x/text)` | Empty string |

After `assign foo foobarblah`, `$sar(r/oo/ee/foo)` returns `feebarblah` and changes `foo` to `feebarblah`.

Registration and `function_sar()`: [source/functions.c](../../source/functions.c). Delimiter parsing, comparisons, and replacement: [source/ircaux.c](../../source/ircaux.c), `next_in_div_list()`, `my_strnicmp()`, and `substitute_string()`. Variable lookup and assignment: [source/alias.c](../../source/alias.c).
