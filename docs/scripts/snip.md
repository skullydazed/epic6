# snip script

## Synopsis

```text
/load snip
$_snippy(<end> "<substring>" <string>)
$snip(<string>)
$rsnip(<string>)
$lsnip(<string>)
$bsnip(<string>)
```

## Description

The `_snippy` function returns a string after repeatedly removing a matching substring from one end. Use `l` to remove it from the start (left), or `r` to remove it from the end (right). It stops at the first end segment that does not match; occurrences inside the remaining string are unchanged.

Quote the substring when it includes spaces so it is parsed as one argument. The remaining text is the input string. This removes repetitions of the supplied substring as a unit, rather than stripping an arbitrary set of characters.

The convenience functions use a single ordinary space as the substring:

| Function | Result |
| --- | --- |
| `$snip(string)` | Remove trailing spaces; identical to `rsnip`. |
| `$rsnip(string)` | Remove trailing spaces. |
| `$lsnip(string)` | Remove leading spaces. |
| `$bsnip(string)` | Remove leading and trailing spaces. |

These wrappers do not remove tabs or every Unicode whitespace character. The input is returned as text; they do not modify a named variable or print their result automatically.

## Matching and limits

Use a nonempty substring. There is no empty-substring check, and an empty value can make the loop fail to make progress. There is no iteration limit or diagnostic for that case.

End-segment comparison uses the script expression `==`, which is case-insensitive for ordinary text. Consequently, a substring can trim differently capitalized text too. The end selector is also permissive: the script uses wildcard `match` against `l`, and anything not selecting left falls back to right. Use literal `l` or `r` rather than relying on invalid values being rejected.

The loop measures lengths with the byte-count `@` operator, while helpers such as `left`, `right`, and `rest` use Unicode code-point counts. ASCII delimiters, including the convenience wrappers' space, avoid that mismatch. A multibyte delimiter can fail to match or remove the intended substring; this is not a reliable general Unicode trimming interface.

The functions perform no file access, external command, or IRC request. There are no settings, helper-script dependencies, or optional platform requirements. Loading defines global aliases and can replace existing aliases of the same names. Calling them as ordinary commands does not automatically display a function result; use them in expressions or `echo`.

## Examples

```text
/load snip
/echo [$_snippy(l "ez " ez ez ezIIIIIez ez ez)]
/echo [$_snippy(r " ez" ez ez ezIIIIIez ez ez)]
/echo [$rsnip(this is a test string    )]
/echo [$lsnip(    this is a test string)]
/echo [$bsnip(  this is a test string  )]
```

The first two results are `[ezIIIIIez ez ez]` and `[ez ez ezIIIIIez]`. The final three each produce `[this is a test string]`. Brackets make the retained edges visible.

## History

These functions were written by Xavier and first appeared in EPIC5-0.3.2. The retained source is marked as 2006 work and thanks kreca and hop for input.

## Source references

- [script/snip](../../script/snip): trimming loop, argument list, wrappers, and examples.
- [source/alias.c](../../source/alias.c): quoted-word alias argument parsing.
- [source/expr.c](../../source/expr.c): `@` expands to byte length.
- [source/expr2.c](../../source/expr2.c): ordinary string equality uses case-insensitive comparison.
- [source/functions.c](../../source/functions.c): `left`, `right`, `rest`, and `chop` implement the selected-end operations.
