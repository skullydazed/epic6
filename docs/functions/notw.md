# $notw()

## Synopsis

`$notw(<number> <word list>)`

## Technical

Returns the word list with word `number` omitted, counting from zero. A missing number returns the empty string. A negative number returns the entire remaining text unchanged, without the word-extraction trimming used for nonnegative indexes.

The number uses base-detecting integer conversion, including hexadecimal and leading-zero octal; nonnumeric text converts to zero and therefore removes the first word. The index is stored as a C `int`. Everything remaining after the number is the word list.

Word boundaries honor [/xdebug dword](../commands/xdebug.md). With it enabled, a double-quoted group counts as one word and can be removed as a unit; with it disabled, whitespace separates words even inside quotes. Retained quotation marks are not removed.

For a nonnegative index, the function extracts the portions before and after that word. Leading and trailing whitespace are subject to trimming. When both portions remain, they are joined with one space, removing extra separators around the removed word. Spacing inside either retained portion is preserved. An ordinary out-of-range positive index leaves all available words, subject to this trimming.

## Practical

Use this to remove a word by its position without modifying the source variable. [$findw()](findw.md) can locate a literal word, but check its result before calling `$notw()`: a negative lookup result removes nothing. [$midw()](midw.md) instead extracts a range of words.

## Returns

The remaining word list, or the empty string when the number is missing, the list is empty, or its only word is removed. A negative index returns the supplied remaining text unchanged.

## Restrictions

The extraction helper reserves `32767` as an end marker. Avoid indexes at or above `32766`, where the adjacent positions calculated by this function can encounter that marker; also avoid values whose `number + 1` exceeds the C `int` range. Ordinary small nonnegative indexes have the behavior described above.

## Examples

| Expression | Result |
| --- | --- |
| `$notw(0 hello there how are you?)` | `there how are you?` |
| `$notw(2 hello there how are you?)` | `hello there are you?` |
| `$notw(-1 hello there how are you?)` | `hello there how are you?` |
| `$notw(1 hello   there   bob)` | `hello bob` |
| `$notw(7 hello there bob)` | `hello there bob` |
| `$notw(0 hello)` | Empty string |

With `xdebug dword` enabled, `$notw(1 one "two words" three)` returns `one three`.

## History

This function first appeared in “plus-2” (post-ircII, pre-EPIC).

Registration and `function_notw()`: [source/functions.c](../../source/functions.c). `extractfw` and the end marker: [include/words.h](../../include/words.h). Word extraction: [source/words.c](../../source/words.c). Integer conversion and joining: [include/functions.h](../../include/functions.h) and [source/ircaux.c](../../source/ircaux.c).
