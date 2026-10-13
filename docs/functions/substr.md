# substr

## Synopsis

`$substr(<substring> <search string>)`

## Technical

Searches for a literal substring and returns its zero-based position in the search string. Matching is case-insensitive, using EPIC's Unicode uppercase mapping rather than wildcard matching. The substring is always a quoted-word argument: use quotation marks to search for spaces. The remaining argument is the search string, with literal quotation marks retained.

Positions count Unicode code points for valid UTF-8, rather than bytes or display columns. Formatting and control characters are included in the text and position count. An ASCII substring normally returns its first occurrence.

## Practical

Use this function to search server output or other text without constructing a wildcard pattern such as [`match`](match.md)'s `*substring*`. Its returned position can be used with [`mid`](mid.md), [`left`](left.md), or [`rest`](rest.md).

[`rsubstr`](rsubstr.md) is intended to search in the opposite direction, but has a separate implementation defect; consult its restrictions before using it.

## Returns

The zero-based position of a match, or `-1` if none is found. An empty substring or empty search string returns `-1`. Completely missing input returns the empty string. There are no options or server requirements.

## Restrictions

The current implementation passes the substring's byte length to a comparison that counts code points. For a valid multibyte UTF-8 substring, this compares beyond the substring's end: an otherwise matching occurrence followed by additional text fails. A matching occurrence at the end can succeed. For example, searching for `é` in `café` returns `3`, while searching in `caféine` returns `-1`. Avoid relying on ordinary substring semantics for multibyte search text until this length mismatch is fixed.

Case matching is based on uppercase code-point mapping, not full Unicode case folding or normalization. Malformed UTF-8 is not validated, and positions in malformed text need not be meaningful.

## Examples

```irc
$substr(foo xFOOyfoo)           Returns 1.
$substr("two words" one two words three)    Returns 4.
$substr(x éx)                  Returns 1 for UTF-8 text.
$substr(z abc)                 Returns -1.
$substr("" abc)                Returns -1.
```

## Source

Handler: [`source/functions.c`](../../source/functions.c:5581). Search and comparison: [`stristr`](../../source/ircaux.c:1250), [`utf8_strnicmp`](../../source/ircaux.c:1601). Position counting: [`quick_code_point_index`](../../source/wcwidth.c:623).
