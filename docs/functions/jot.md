# $jot()

## Synopsis

`$jot(<start> <stop> [<interval>])`

## Technical

Creates a space-separated sequence of numbers from `<start>` toward `<stop>`. Start and stop are required; omitting either returns the empty string. The interval defaults to `1`. A zero interval returns the empty string; a negative interval is replaced by its absolute value.

All three arguments use `atof` conversion to C `double`, so fractional steps and scientific notation are supported. Nonnumeric text converts to zero, and numeric prefixes may be accepted despite trailing text. Additional arguments are ignored.

If start is less than stop, the interval is added repeatedly while the current value is less than or equal to stop. Otherwise it is subtracted while the value is greater than or equal to stop. Equal endpoints produce one value with an ordinary nonzero finite interval. The endpoint is included only if the sequence reaches it without passing it.

Values are first formatted with `%f` (six fractional decimal places). With [floating_point_math](../settings/floating_point_math.md) enabled, trailing fractional zeros and the decimal point are removed. With that setting disabled, the decimal point and everything after it are discarded, even though sequence generation still uses doubles. [floating_point_precision](../settings/floating_point_precision.md) does not control this formatting. Floating-point rounding can omit an expected endpoint or make distinct small steps print as identical values.

## Practical

Use [fe](../commands/fe.md) or [for](../commands/for.md) list iteration to process the returned sequence. Passing an integer range to [chr](chr.md) can construct a range of characters.

## Returns and restrictions

Returns every generated value as a word, or the empty string for missing required arguments or a zero interval. It does not report conversion, range, or floating-point errors separately.

There is no output-count limit or event-loop yielding inside the generation loop. Keep ranges reasonably small and use finite endpoints with a step large enough to change the counter. Infinite values or a step too small to affect the current floating-point value can prevent termination. Each formatted value also has a fixed buffer limit of 98 characters, so very large fixed-point representations can be truncated.

## Examples

| Expression | Result |
| --- | --- |
| `$jot(2 6)` | `2 3 4 5 6` |
| `$jot(2 7 2)` | `2 4 6` |
| `$jot(3 -2)` | `3 2 1 0 -1 -2` |
| `$jot(4)` | Empty string |
| `$jot(4 6 8)` | `4` |
| `$jot(a 4)` | `0 1 2 3 4` |
| `$jot(0 1 0.25)` | `0 0.25 0.5 0.75 1` with `floating_point_math` enabled; `0 0 0 0 1` otherwise |
| `$jot(2 6 -2)` | `2 4 6` |
| `$jot(2 6 0)` | Empty string |

## Source

Registered and implemented as `function_jot` in [source/functions.c](../../source/functions.c). Argument conversion is in [include/functions.h](../../include/functions.h), and numeric text cleanup (`canon_number`) is in [source/expr.c](../../source/expr.c).
