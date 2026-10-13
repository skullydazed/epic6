# $sin()

## Synopsis

`$sin(<number>)`

## Technical

Returns the sine of the angle given in radians. Finite real inputs have results between `-1` and `1`. [$asin()](asin.md) returns a principal inverse angle.

The first function word is converted with `atof()`, accepting decimal fractions and scientific notation. Additional words are ignored. Missing input returns empty. Nonnumeric input becomes zero, and a numeric prefix can be accepted before trailing nonnumeric text. See the [rules about number arguments](../rules_about_number_arguments.md).

## Returns

The calculation uses the C library's `sin()` and a C `double`. Results are formatted with `%.50g`, rather than limited to six decimal places. The [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting. Extra printed digits do not mean 50 digits of accuracy.

The wrapper clears `errno` after argument conversion, then returns `DOM` for a library-reported domain error, `RANGE` for a reported range error, or empty for another reported error. It checks `errno`, not floating-point exception flags.

Infinite input causes a library domain error, ordinarily producing NaN; EPIC returns `DOM` when that error is reported through `errno`, but can return a NaN string when only exception flags report it. NaN input ordinarily produces a NaN result. These special-input semantics follow the [C library sine interface](https://www.man7.org/linux/man-pages/man3/sin.3.html).

## Examples

| Expression | Result |
| --- | --- |
| `$sin(0)` | `0` |
| `$sin(1)` | Approximately `0.8414709848078965` |
| `$sin(-1)` | Approximately `-0.8414709848078965` |
| `$sin()` | Empty string |

Approximate examples describe numeric values; returned strings may contain more digits. There are no options or IRC connection requirements.

Registration, `MATH_FUNCTION(function_sin, sin)`, and error mapping: [source/functions.c](../../source/functions.c). Argument conversion and return formatting: [include/functions.h](../../include/functions.h).
