# right function

## Synopsis

`$right(<count> <text>)`

`$right({"number":<count>,"text":"<text>"})`

## Technical

In the ordinary form, returns the last `count` Unicode code points of the text. If the text is no longer than the count, returns the whole text. Missing count or text, a negative count, or zero returns the empty string.

The count uses normal integer argument parsing. All remaining input is literal text, without further word splitting or quote removal. Spaces, quotes, and formatting controls are included. The function counts code points rather than bytes, display columns, or graphemes, and does not change the input variable.

Input beginning with `{` selects the JSON form, with fields `number` and `text`. **The current JSON implementation is defective:** it checks the parsed text for emptiness but then counts and extracts from the original JSON argument string. It therefore returns a suffix of that JSON string, or the whole JSON string for a sufficiently large count, rather than the requested text. Use the ordinary form for text extraction. Missing fields return empty; malformed JSON prints a parser diagnostic and returns empty.

## Practical

Use this function to extract a trailing part of a fixed-format string. [$after()](after.md) is more direct when selecting text after a delimiter, and [$mid()](mid.md) selects by start and length. [$rightw()](rightw.md) selects trailing words.

## Returns

The selected suffix without padding. There are no numeric error returns or normal screen output, and no IRC connection is required. [$pad()](pad.md) can add padding, but its width is measured in display columns, which may differ from the code-point count used here.

## Restrictions

Supply valid UTF-8. The preliminary quick count and traversal handle malformed byte sequences differently: invalid bytes encountered during traversal count as one skipped byte. Results on malformed text are not a reliable character suffix. Combining marks and emoji components are counted separately.

## Examples

| Expression | Result |
| --- | --- |
| `$right(5 biklmnopstv)` | `opstv` |
| `$right(15 biklmnopstv)` | `biklmnopstv` |
| `$right(-2 biklmnopstv)` | Empty string. |
| `$right(0 hello)` | Empty string. |
| `$right(2 café)` | `fé` |

## History

First appeared in ircII.

## Source

Registration, `function_right()`, and JSON parsing: [source/functions.c](../../source/functions.c). Unicode counting and decoding: [source/wcwidth.c](../../source/wcwidth.c). Integer parsing: [include/functions.h](../../include/functions.h).
