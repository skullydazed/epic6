# $stricmp()

## Synopsis

`$stricmp(<string1> <string2>)`

## Technical

Compares two strings case-insensitively and returns an integer comparison result. Both arguments are double-quoted words, so quotes group strings containing spaces regardless of [/xdebug dword](../commands/xdebug.md). Additional arguments are ignored. Quote grouping removes the outer quotes before comparison.

Comparison decodes UTF-8 codepoints and compares their uppercase mappings. EPIC uses a dedicated `en_US.UTF-8` locale for those mappings when it can create one. If that locale is unavailable, non-ASCII codepoints are left unchanged and only ASCII case conversion is applied. This is not locale-aware dictionary collation, full Unicode case folding, or Unicode normalization. Server-specific IRC casemapping is not used.

When either decoded string ends, the implementation falls back to subtracting the current raw `char` bytes. This can make prefix ordering involving non-ASCII bytes depend on platform `char` signedness. Use valid UTF-8; malformed bytes can be skipped during decoding and do not produce a separate validation error.

## Practical

Use `== 0` to test case-insensitive equality. For ordering, test the result's sign rather than expecting exactly `-1` or `1`. [$sort()](sort.md) uses this comparison, and [$sortby()](sortby.md) can call the built-in directly as a comparator.

## Returns

| Result | Meaning |
| --- | --- |
| Negative | First string compares before the second |
| `0` | Strings compare equal |
| Positive | First string compares after the second |

Missing either argument returns the empty string instead of a comparison integer. Explicit empty quoted strings are valid arguments. No options, variable changes, or IRC connection requirements apply.

## Examples

| Expression | Result |
| --- | --- |
| `$stricmp(Hello HELLO)` | `0` |
| `$stricmp(apple banana)` | Negative |
| `$stricmp(banana apple)` | Positive |
| `$stricmp("one two" "ONE TWO")` | `0` |
| `$stricmp("" "")` | `0` |
| `$stricmp(hello)` | Empty string |

Registration and `function_stricmp()`: [source/functions.c](../../source/functions.c). `my_stricmp()` and UTF-8 comparison: [source/ircaux.c](../../source/ircaux.c). Locale creation and uppercase mapping: [source/recode.c](../../source/recode.c). Double-quoted parsing: [include/functions.h](../../include/functions.h).
