# cosh function

## Synopsis

```text
$cosh(number)
```

## Technical

Returns the hyperbolic cosine of `number`, equivalent mathematically to `(exp(number) + exp(-number)) / 2`. Every finite real number is in its domain; successful real results are at least 1. The corresponding principal inverse is [`acosh()`](acosh.md).

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Missing input returns an empty string. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text.

## Returns and restrictions

The calculation uses the C library's `cosh()` and a C `double`. Results are formatted with `%.50g`, not limited to six decimal places. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change the calculation or formatting. Extra displayed digits do not imply 50 digits of accuracy.

Large-magnitude finite inputs can overflow. EPIC returns `RANGE` if the library reports the overflow through `errno`; platforms that report it only through floating-point exception flags can instead return the library's overflow value, commonly infinity. Positive or negative infinite input produces positive infinity, and NaN input ordinarily produces NaN. The shared wrapper returns `DOM` for a reported domain error and an empty string for another reported error. It checks `errno`, not floating-point exception flags, and clears `errno` after argument conversion and before calculating the hyperbolic cosine.

## Examples

| Expression | Result |
| --- | --- |
| `$cosh(0)` | `1` |
| `$cosh(1)` | Approximately `1.5430806348152437` |
| `$cosh(-1)` | Approximately `1.5430806348152437` |
| `$cosh()` | Empty string |

The approximate examples describe numeric values; the returned strings may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `cosh` and implements it through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX hyperbolic cosine specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/cosh.html) defines special inputs, overflow, and library error reporting.
