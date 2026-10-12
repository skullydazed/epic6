# $log10()

## Synopsis

`$log10(<number>)`

## Technical

Returns the base-ten logarithm of `<number>`: the value `x` such that `10` raised to `x` equals the input. The real-valued domain is positive numbers. See [log](log.md) for natural logarithms.

The first function argument is converted with `atof`, accepting decimal fractions and scientific notation. Additional arguments are ignored. Missing input returns the empty string. Nonnumeric text converts to zero, and numeric prefixes may be accepted despite trailing text.

The calculation uses the C library's `log10` and a C `double`. Results are formatted with `%.50g`, not limited to six decimal places. The [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md) settings do not affect the calculation or formatting. Extra displayed digits do not imply 50 digits of accuracy.

## Returns and restrictions

The calculated logarithm when `errno` is zero. A reported `EDOM` becomes `DOM`, `ERANGE` becomes `RANGE`, and another reported error becomes the empty string. The wrapper clears `errno` after argument conversion and before calculating.

Negative inputs produce a domain error; zero is a logarithmic pole. EPIC checks only `errno`, not floating-point exception flags, so platforms reporting errors only through exception flags can return library values such as NaN or negative infinity instead of `DOM` or `RANGE`. Positive infinity and NaN inputs ordinarily produce infinity and NaN, respectively. These follow the platform C math library's behavior.

## Examples

| Expression | Result |
| --- | --- |
| `$log10(1)` | `0` |
| `$log10(10)` | `1` |
| `$log10(100)` | `2` |
| `$log10(2)` | Approximately `0.3010299956639812` |
| `$log10()` | Empty string |

The approximate example describes the numeric value; the returned string may contain more digits.

## Source

Registration, `MATH_FUNCTION`, and `MATH_RETVAL` are in [source/functions.c](../../source/functions.c). Argument conversion and floating-point return formatting are in [include/functions.h](../../include/functions.h).
