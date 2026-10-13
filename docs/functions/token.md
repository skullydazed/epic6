# token

## Synopsis

`$token(<variable> <delimiter>)`

## Technical

Removes and returns the prefix of a variable's value before the first case-insensitive occurrence of `<delimiter>`. The delimiter itself is discarded, and the text after it is assigned back to the variable. It is a literal substring, not a character set, wildcard, word index, or numeric token number.

The variable name is the first function word, uppercased before lookup. The entire remaining argument is the delimiter, including spaces and literal quotation marks. There is no additional argument parsing. Lookup and writeback use normal variable scope rules; an existing local variable is updated, otherwise normal global assignment applies.

If the delimiter is not found, the whole current value is returned and the variable is assigned an empty remainder. An empty delimiter also follows this path. Empty or undefined variables return empty without writeback.

## Practical

Use repeated calls to consume simple delimiter-separated ASCII text from a variable. Consecutive delimiters produce empty returned fields; a delimiter at the beginning returns empty while still advancing the variable. A trailing delimiter clears the remainder. Inspect the variable as well as the return value if empty fields matter.

## Returns

The text before the first match, or the whole value if no match exists. Missing variable input, an empty value, or a delimiter at the beginning returns the empty string. Empty global writeback removes the variable; an existing local can remain defined with an empty value. There are no options or server requirements.

## Restrictions

The current implementation obtains a code-point position from the search helper but uses it as a byte position when splitting the value. Multibyte UTF-8 before the delimiter can therefore produce an incorrect split or broken UTF-8. The search helper also compares a multibyte delimiter using its byte length as a code-point limit, causing ordinary matches followed by more text to fail. Use ASCII values and delimiters for reliable behavior until these mismatches are fixed; see [`substr`](substr.md) for the search limitation.

Case matching uses uppercase code-point mapping, not full Unicode case folding or normalization. The function changes the variable even on a failed search, so save a copy first if the original value is needed.

## Examples

```irc
assign fields one::three
$token(fields :)    Returns one; fields becomes :three.
$token(fields :)    Returns empty; fields becomes three.
$token(fields :)    Returns three; fields is cleared.
```

For a delimiter containing spaces, pass those spaces as the remaining argument rather than quoting it: `$token(fields two words)` searches for the literal substring `two words`.

## Source

Handler and writeback: [`source/functions.c`](../../source/functions.c:9546). Search: [`stristr`](../../source/ircaux.c:1250). Variable lookup and assignment: [`source/alias.c`](../../source/alias.c:1059).
