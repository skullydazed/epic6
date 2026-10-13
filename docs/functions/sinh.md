# $sinh()

## Synopsis

`$sinh(<number>)`

## Technical

Returns the hyperbolic sine of the number, mathematically `(exp(x) - exp(-x)) / 2`. It accepts real inputs of either sign and is not bounded between `-1` and `1`. [$asinh()](asinh.md) is the inverse hyperbolic sine.

The first function word is converted with `atof()`, accepting decimal fractions and scientific notation. Additional words are ignored. Missing input returns empty. Nonnumeric input becomes zero, and numeric prefixes can be accepted before trailing nonnumeric text. See the [rules about number arguments](../rules_about_number_arguments.md).

## Returns

The calculation uses the C library's `sinh()` and a C `double`. Results are formatted with `%.50g`, rather than limited to six decimal places. The [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting. Extra printed digits do not imply 50 digits of accuracy.

The wrapper clears `errno` after argument conversion and returns `RANGE` for a library-reported range error, `DOM` for a reported domain error, or empty for another reported error. Large finite inputs can overflow. EPIC checks `errno`, not floating-point exception flags; if overflow is reported only through exception flags, it can return an infinity string instead of `RANGE`.

NaN input ordinarily produces NaN, and positive or negative infinity input produces infinity of the same sign. Signed zero is preserved by the library. These special-input and overflow semantics follow the [C library hyperbolic sine interface](https://www.man7.org/linux/man-pages/man3/sinh.3.html).

## Examples

| Expression | Result |
| --- | --- |
| `$sinh(0)` | `0` |
| `$sinh(1)` | Approximately `1.1752011936438014` |
| `$sinh(-1)` | Approximately `-1.1752011936438014` |
| `$sinh()` | Empty string |

Approximate examples describe numeric values; returned strings may contain more digits. There are no options or IRC connection requirements.

Registration, `MATH_FUNCTION(function_sinh, sinh)`, and error mapping: [source/functions.c](../../source/functions.c). Argument conversion and return formatting: [include/functions.h](../../include/functions.h).
