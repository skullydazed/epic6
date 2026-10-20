# Pattern matching

Any string can be a pattern. A literal pattern matches that text; wildcard patterns describe a range of possible strings. EPIC uses this matcher in hooks, wildcard-selection functions, array searches, and the expression operators `=~` and `!~`. Regular expressions and filename globbing are separate interfaces and do not necessarily use these rules.

## Wildcards

| Character | Meaning |
| --- | --- |
| `*` | Zero or more bytes, including spaces. |
| `%` | Zero or more bytes without crossing a literal ASCII space. |
| `?` | Exactly one byte. |
| `\character` | Match the following character literally instead of treating it as a wildcard. A trailing backslash without a following character fails. |

Matching covers the whole string, not an arbitrary substring. Use `*text*` to search for text anywhere. Literal comparisons use byte-oriented case folding, so matching is case-insensitive for ordinary letters but is not Unicode-aware case folding. In UTF-8, one non-ASCII character can require more than one `?`.

`%` is not a general “non-whitespace” class: its boundary is the space byte, not every tab, newline, or Unicode spacing character. There is no regular-expression bracket class such as `[a-z]` in this wildcard syntax; ordinary brackets have no such meaning.

With the input `hello there`:

| Pattern | Matches? |
| --- | --- |
| `hello*` | Yes. |
| `hello%` | No: `%` cannot cross the space. |
| `hello%?%` | Yes: `?` consumes the space. |
| `h?llo?th?r?` | Yes. |
| `hello` | No: unmatched text remains. |

For example:

```irc
@ matched = [hello there] =~ [hello%?%]
```

This assigns `1`. `!~` returns the opposite Boolean result. See [expressions](expressions.md).

## Branches

Patterns can contain alternative branches inside `\[ ... \]`. At the matcher level, these delimiters contain one backslash each:

```text
\[foo bar\]blah
```

This matches `fooblah` or `barblah`. Alternatives are read as words, with double-quoted alternatives available for text containing spaces. Each alternative is substituted into the surrounding pattern and matched using the same wildcard rules. Multiple branch groups can combine, and the implementation recursively expands branches.

The calling parser may process backslashes before the pattern reaches the matcher. Preserve the backslashes required at that stage; do not blindly copy matcher-level notation into every command context. A single-quoted expression operand avoids expando substitution:

```irc
@ matched = [fooblah] =~ '\[foo bar\]blah'
```

This assigns `1`. A branch can be used wherever the shared wildcard matcher is used, including applicable hook patterns and wildcard functions; this does not imply support in regular-expression or filesystem-glob interfaces. Keep branch delimiters balanced rather than relying on malformed-pattern fallback behavior.

## Match scores and result conventions

Internally, failure returns zero. A successful match scores one plus the number of literal bytes matched; bytes consumed by `*`, `%`, or `?` do not add specificity. Branch expansion returns the highest successful branch score. Some callers use this score to select the most specific matching pattern.

The public result depends on the caller: `=~` returns a Boolean, [match](../functions/match.md) returns a selected word position, and [matchitem](../functions/matchitem.md) returns an array item number. Do not interpret every matching function's result as the internal score or assume that zero means failure for every interface. Hook selection also has its own serial-number and tie rules; see [on](../commands/on.md).

A very complex wildcard match can hit the matcher's iteration guard and return failure with a diagnostic. Many alternatives can also multiply recursive matching work. Prefer specific, simple patterns when processing frequent events. Matching itself is local and requires neither a server request nor a script package; the surrounding command can have additional effects.

## Source references

- `source/reg.c`: `new_match()` wildcard handling, byte comparisons, scoring, iteration guard, and `wild_match()` branches.
- `source/expr2.c`: `=~` and `!~` Boolean results.
- `source/functions.c` and `source/array.c`: wildcard-function callers and their return conventions.
- `source/hook.c`: hook matching and selection.
