# insert function

## Synopsis

`$insert(<position> <word> [<text>])`

## Technical

Returns `text` with `word` inserted at the zero-based character position. The insertion precedes the character at that position: position `3` preserves the first three characters, then inserts the word. No spaces are added automatically.

For valid UTF-8, position counts Unicode code points rather than bytes or display columns. Zero or a negative position inserts at the beginning. A position at or beyond the end appends the word; it does not pad the text.

Position is an integer function argument using base-detecting conversion. Nonnumeric text converts to zero. The inserted word is always double-quote-aware, independently of `/xdebug dword`; quote it to insert spaces or multiple words. Text is the entire remaining input, including additional separator spaces and literal quotation marks. Omitting text returns the inserted word itself; missing position or inserted-word argument returns empty.

## Practical

Use this function to insert text in the middle of a string at a known location, perhaps obtained from [$index()](index.md) or [$rindex()](rindex.md). It returns a new string without changing a variable or the visible input line. Store the result explicitly if needed. [$insertw()](insertw.md) instead operates on word positions.

## Returns

The concatenated prefix, inserted word, and remaining suffix. No error code or diagnostic is produced for an out-of-range position; it is clamped to an end as described above. Missing required arguments returns empty.

## Examples

| Expression | Result |
| --- | --- |
| `$insert(3 baz foobarbooya)` | `foobazbarbooya` |
| `$insert(0 X abc)` | `Xabc` |
| `$insert(-1 X abc)` | `Xabc` |
| `$insert(100 X abc)` | `abcX` |
| `$insert(3 " and " abcdef)` | `abc and def` |
| `$insert(1 X élan)` | `éXlan` |
| `$insert(0 hello)` | `hello` |

## Restrictions

Use valid UTF-8 for predictable character boundaries. Invalid bytes are skipped while locating the insertion point, rather than counted as normal characters.

## Source

Registration, argument handling, position traversal, and concatenation: [source/functions.c](../../source/functions.c). Integer and quoted-word argument macros: [include/functions.h](../../include/functions.h). UTF-8 decoding: [source/wcwidth.c](../../source/wcwidth.c). String concatenation: [source/ircaux.c](../../source/ircaux.c).
