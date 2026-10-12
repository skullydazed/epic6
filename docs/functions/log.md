# $log()

## Synopsis

`$log(<number>)`

## Technical

Returns the natural logarithm of `<number>`: the value `x` such that `exp(x)` equals the input. The real-valued domain is positive numbers. See [exp](exp.md) for the inverse operation and [log10](log10.md) for base-ten logarithms.

The first function argument is converted with `atof`, accepting decimal fractions and scientific notation. Additional arguments are ignored. Missing input returns the empty string. Nonnumeric text converts to zero, and a numeric prefix can be accepted despite trailing text.

The calculation uses the C library's `log` and a C `double`. Results are formatted with `%.50g`, not limited to six decimal places. The [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md) settings do not affect the calculation or formatting. Extra displayed digits do not imply 50 digits of accuracy.

## Returns and restrictions

A calculated number when the library leaves `errno` at zero. A reported `EDOM` becomes `DOM`, `ERANGE` becomes `RANGE`, and another reported error becomes the empty string. The wrapper clears `errno` after argument conversion and before calculating.

Negative inputs produce a domain error; zero is a logarithmic pole. Error signaling can use `errno` or floating-point exception flags, but EPIC checks only `errno`. Consequently, invalid inputs can return library values such as NaN or negative infinity instead of `DOM` or `RANGE` on platforms that report only through exception flags. Positive infinity and NaN inputs ordinarily produce infinity and NaN, respectively. These are C-library behaviors; see the [ISO C draft's math-library specification](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1548.pdf).

This function calculates a logarithm; it does not control logging. See the [log command](../commands/log.md) for log destinations.

## Examples

| Expression | Result |
| --- | --- |
| `$log(1)` | `0` |
| `$log(2)` | Approximately `0.6931471805599453` |
| `$log(10)` | Approximately `2.302585092994046` |
| `$log()` | Empty string |

The approximate examples describe numeric values; returned strings may contain more digits.

## Source

Registration, `MATH_FUNCTION`, and `MATH_RETVAL` are in [source/functions.c](../../source/functions.c). Argument conversion and floating-point return formatting are in [include/functions.h](../../include/functions.h).
