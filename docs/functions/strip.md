# $strip()

## Synopsis

`$strip(<chars> <text>)`

## Technical

Returns a copy of the text with every occurrence of any character in the supplied set removed. [$pass()](pass.md) performs the complementary operation, retaining only members of its set.

The character set is always a double-quoted word, independently of [/xdebug dword](../commands/xdebug.md). Use `" "` to remove spaces. Everything remaining after the set is text, including additional words and literal quotation marks; the text is not separately dequoted.

Membership is an exact, case-sensitive Unicode codepoint comparison. The set is literal: `a-z` means `a`, `-`, and `z`, and `^` does not invert it. Repeating a character in the set does not change the result.

Remaining codepoints keep their order and repetitions. Whitespace and formatting controls remain unless included in the set. Combining marks are independent codepoints; comparison does not normalize visually equivalent Unicode spellings. Valid retained codepoints are encoded back to UTF-8. The decoder can skip malformed bytes, so this is not a byte-preserving operation for invalid UTF-8.

## Practical

Use this to remove selected unwanted characters from text. For removing terminal or IRC formatting sequences as a unit, use the appropriate formatting-strip function rather than merely deleting its introducer character; deleting an introducer alone can leave its parameters behind. See [$stripansi()](stripansi.md), [$stripc()](stripc.md), and [$stripcrap()](stripcrap.md).

## Returns

Text with all members of the character set removed. Missing input, missing/empty text, or removal of every codepoint returns empty. An explicitly empty quoted set preserves valid text. The function returns a new string without assigning a variable. There are no options, separate error codes, or IRC connection requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$strip(aeoiu hello there)` | `hll thr` |
| `$strip(f free fine frogs)` | `ree ine rogs` |
| `$strip(" " free fine frogs)` | `freefinefrogs` |
| `$strip(^ a^b)` | `ab` |
| `$strip(é café)` | `caf` |
| `$strip("" a b)` | `a b` |

## History

First appeared in ircII-2.1.5.

Registration and `function_strip()`: [source/functions.c](../../source/functions.c). Double-quoted argument parsing: [include/functions.h](../../include/functions.h). UTF-8 decoding and encoding: [source/wcwidth.c](../../source/wcwidth.c).
