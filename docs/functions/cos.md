# cos function

## Synopsis

```text
$cos(number)
```

## Technical

Returns the cosine of `number`, interpreted as an angle in radians. Finite real inputs have results between -1 and 1. The corresponding inverse operation is [`acos()`](acos.md), which returns a principal angle.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Missing input returns an empty string. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text.

## Returns and restrictions

The calculation uses the C library's `cos()` and a C `double`. Results are formatted with `%.50g`, not limited to six decimal places. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change the calculation or formatting. Extra displayed digits do not imply 50 digits of accuracy.

Infinite input causes a library domain error. EPIC returns `DOM` if the library reports it through `errno`; platforms that report it only through floating-point exception flags can instead return a NaN string. NaN input ordinarily produces a NaN result. The shared wrapper returns `RANGE` for a reported range error and an empty string for another reported error. It checks `errno`, not floating-point exception flags, and clears `errno` after argument conversion and before calculating the cosine.

## Examples

| Expression | Result |
| --- | --- |
| `$cos(0)` | `1` |
| `$cos(1)` | Approximately `0.5403023058681398` |
| `$cos(-1)` | Approximately `0.5403023058681398` |
| `$cos()` | Empty string |

The approximate examples describe numeric values; the returned strings may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `cos` and implements it through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX cosine specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/cos.html) defines radians, special inputs, and library error reporting.
