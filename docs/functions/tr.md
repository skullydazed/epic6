# tr

## Synopsis

`$tr(/<input>/<output>/<string>)`

## Technical

Translates individual Unicode code points in `<string>` by pairing the first character of `<input>` with the first of `<output>`, the second with the second, and so on. Matching is exact and case-sensitive. Unlisted characters are retained; repeated input characters use their first listed mapping.

If the output list is shorter, its final character is reused for remaining input characters. An empty output list deletes matching characters. An empty input list leaves valid text unchanged. Extra output characters have no effect. Translation is one pass: generated characters are not translated again.

The first code point of the argument is the delimiter. It need not be `/`, but cannot occur in either character list. The next two occurrences separate the lists and text; all remaining text is literal, including spaces, quotation marks, and further delimiters. There is no closing delimiter: a trailing slash after the text is part of the text. There is no quoting or escaping mechanism for a delimiter inside either list.

Character lists do not expand ranges, complement sets, or interpret escapes. For example, `a-z` lists three literal characters, and `^` has no inversion meaning. Variables and function calls can supply arguments through normal expression expansion.

## Practical

Use explicit character pairs for simple substitutions or ROT13-style translation. Use [`strip`](strip.md) when only deleting a character set. Translation is not a shell-escaping or general sanitization interface.

## Returns and restrictions

The translated string. Empty input, a missing list delimiter, or empty text returns empty. Invalid UTF-8 bytes can be skipped during decoding; this is not a binary-data operation. UTF-8 replacement code points can occupy a different byte length from their originals. No variables are changed, and there are no options, settings, or server requirements.

## Examples

```irc
$tr(/s/z/efnet has bots)          Returns efnet haz botz.
$tr(/aeiouy/yuoiea/efnet has bots) Returns ufnut hys bits.
$tr(/aeiouy/_/efnet has bots)     Returns _fn_t h_s b_ts.
$tr(/abc//blah blah)             Returns lh lh.
$tr(#a#e#blah)                   Returns bleh.
$tr(/é/e/café)                   Returns cafe for UTF-8 text.
```

## Source

Registration and translation: [`source/functions.c`](../../source/functions.c:4469). Unicode decoding and encoding: [`source/wcwidth.c`](../../source/wcwidth.c).
