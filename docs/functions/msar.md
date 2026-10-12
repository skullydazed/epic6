# $msar()

## Synopsis

`$msar([c][i][g][r]/<search>/<replace>/[<search>/<replace>/...]<text>)`

## Technical

Performs multiple literal substring substitutions in order. By default, each pair replaces only its first case-insensitive occurrence. Each later pair operates on the result of the preceding pair, so it can replace text inserted by an earlier substitution. Search strings are not wildcard patterns or regular expressions, and replacements have no backreference syntax.

The input consists of zero or more lowercase option letters, a delimiter, search/replacement pairs separated by that delimiter, and the text following the **final** delimiter. A delimiter can be any valid Unicode code point other than the option letters `c`, `i`, `g`, and `r`; `/` is customary. There is no delimiter escaping. Neither search strings, replacements, nor the final text can contain it. Spaces are literal content, not word separators, and quotation marks do not group or dequote segments.

| Option | Effect |
| --- | --- |
| `c` | Case-sensitive comparison |
| `i` | Case-insensitive comparison, the default |
| `g` | Replace successive nonoverlapping occurrences, up to 99,999 per pair |
| `r` | Treat the final text as a variable name, read its value, and assign the final result back to that variable |

Options can be repeated or reordered. If both `c` and `i` occur, the last one controls case sensitivity. Options are lowercase; an uppercase letter is treated as the delimiter.

Each substitution scans its input once and skips over the matched search text. Its inserted replacement is not searched again by the same pair, even with `g`, so a replacement containing its own search text does not cause repeated expansion within that pair.

## Practical

Use this when several substitutions should be applied to the same string. [$sar()](sar.md) performs a single substitution. With `r`, supply the variable's name, without `$`; the returned value is also assigned using normal [/assign](../commands/assign.md) scope rules. Without `r`, a source variable's value is not changed.

## Returns

The resulting text. Missing input, option letters without a delimiter, or no further delimiter after the opening delimiter returns the empty string. Empty resulting text is also a successful result.

An empty replacement deletes matched text. A trailing search without a replacement is likewise treated as a deletion, rather than being ignored: `$msar(/as/asdf)` returns `df`. An empty search stops processing all remaining pairs, returning the text produced so far. Thus `$msar(//text)` returns `text` unchanged.

## Restrictions

Case-sensitive searches compare bytes. Case-insensitive searches compare Unicode code points using EPIC's uppercase mappings, but scanning and removal still advance by bytes and by the search string's original byte length. Use care with malformed UTF-8 or case-equivalent strings with different byte lengths; this is not Unicode-normalized replacement.

## Examples

| Expression | Result |
| --- | --- |
| `$msar(/as/xy/asdf)` | `xydf` |
| `$msar(/as/xy/yd/42/asdf)` | `x42f` |
| `$msar(/e/he/pi/ll/c/o/epic)` | `hello` |
| `$msar(g/a/aa/aaa)` | `aaaaaa` |
| `$msar(/a//banana)` | `bnana` |
| `$msar(c/FOO/bar/foo)` | `foo` |

After `assign sample banana`, `$msar(gr/a/o/sample)` returns `bonono` and changes `sample` to `bonono`.

Implementation and registration: [source/functions.c](../../source/functions.c), `function_msar()`. Delimiter splitting, case comparison, and replacement: [source/ircaux.c](../../source/ircaux.c), `next_in_div_list()`, `my_strnicmp()`, and `substitute_string()`. Variable lookup and assignment: [source/alias.c](../../source/alias.c).
