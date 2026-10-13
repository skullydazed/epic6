# $pass()

## Synopsis

```text
$pass(<chars> <text>)
$strip(<chars> <text>)
```

## Technical

`pass()` returns the text with every code point **not** present in `chars` removed. [$strip()](strip.md) performs the complementary operation, removing code points present in its character set.

The character set always recognizes double-quoted words, independently of [/xdebug dword](../commands/xdebug.md). Use quotes to include spaces, for example `"abc "`. Everything remaining after that argument is text, including additional words and literal quotation marks; the text is not separately dequoted.

Membership is an exact, case-sensitive Unicode code-point comparison. `chars` is a set of literal characters, not a wildcard pattern or regular expression: `a-z` means the three characters `a`, `-`, and `z`, and `^` does not invert the set. Repeating a character in the set does not duplicate it in the result.

Accepted characters retain their original order and repetitions. Spaces, punctuation, and formatting controls are removed unless their code points are explicitly included. Combining marks are independent code points; matching does not normalize visually equivalent Unicode spellings.

## Practical

Use this to retain a chosen set of characters, such as decimal digits or selected letters, from a string. If you instead need to remove a chosen set, use `$strip()`.

## Returns

The filtered text, without changing the source variable. Missing character-set or text arguments, an explicitly empty character set, or no accepted code points returns the empty string.

Input and the set are decoded with the UTF-8 helper's resynchronization enabled. Malformed bytes are skipped while looking for a decodable sequence, and accepted code points are encoded back to UTF-8. The function therefore does not preserve arbitrary raw bytes or serve as a strict UTF-8 validator.

## Examples

| Expression | Result |
| --- | --- |
| `$pass(aeoiu hello there)` | `eoee` |
| `$strip(aeoiu hello there)` | `hll thr` |
| `$pass(0123456789 room 42, floor 3)` | `423` |
| `$pass(a Aaa)` | `aa` |
| `$pass("a " a b a)` | `a  a` (the spaces are retained) |
| `$pass(é café)` | `é` for UTF-8 text |
| `$pass("" text)` | Empty string |

## History

This function first appeared in ircII 2.1.5.

Registration and `function_pass()`: [source/functions.c](../../source/functions.c). Always-quoted argument parsing: [include/functions.h](../../include/functions.h). UTF-8 decoding, resynchronization, and encoding: [source/wcwidth.c](../../source/wcwidth.c).
