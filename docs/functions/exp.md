# exp function

## Synopsis

`$exp(<number>)`

## Technical

Returns the value of *e* raised to `number`, where *e* is the base of natural logarithms. Every finite real number is in its mathematical domain. The inverse operation for positive values is [$log()](log.md).

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Missing input returns the empty string. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text.

## Returns and restrictions

The calculation uses the C library's `exp()` and a C `double`. Successful results are formatted with `%.50g`, rather than being limited to six decimal places. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change this calculation or formatting. Extra displayed digits do not imply 50 digits of accuracy.

Large positive finite inputs can overflow; sufficiently negative finite inputs can underflow to a very small value or zero. If the library reports a range error through `errno`, EPIC returns `RANGE`. If it does not, EPIC formats the library's returned value instead. Positive infinity ordinarily produces positive infinity, negative infinity produces zero, and NaN produces NaN.

The shared wrapper also returns `DOM` for a reported domain error and the empty string for another reported error. It checks `errno`, rather than floating-point exception flags, and clears `errno` after argument conversion and before calculating the exponential.

## Examples

| Expression | Result |
| --- | --- |
| `$exp(0)` | `1` |
| `$exp(1)` | Approximately `2.718281828459045` |
| `$exp(-1)` | Approximately `0.36787944117144233` |
| `$exp()` | Empty string |

The approximate examples describe numeric values; returned strings may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `exp` and implements it through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. [C floating-point special-value guidance](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1629.htm) discusses infinite inputs, and [POSIX mathematical error rules](https://pubs.opengroup.org/onlinepubs/9799919799/basedefs/V1_chap04.html) describe range-error reporting.
