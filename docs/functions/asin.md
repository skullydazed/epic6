# asin function

## Synopsis

```text
$asin(number)
```

## Technical

`asin()` calls the C library's `asin()` function to return the principal arc sine of `number`. For a number between -1 and 1 inclusive, the result is between -pi/2 and pi/2 radians. It does not return degrees. [`sin()`](sin.md) performs the corresponding sine operation.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Missing input returns the empty string. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text.

## Returns and restrictions

The calculation uses a C `double`, and results are formatted with `%.50g`. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting. Additional displayed rounding digits do not imply 50 digits of accuracy.

Arguments outside [-1, 1] cause a library domain error. EPIC returns `DOM` when the library reports that error through `errno`; if the platform reports it only through a floating-point exception, the result can instead be a NaN string. Very small subnormal inputs can cause a platform-dependent range error. The wrapper returns `RANGE` for a reported range error and the empty string for another reported error. It checks `errno`, not floating-point exception flags. Validate the input range rather than relying on one particular error string.

## Examples

| Expression | Result |
| --- | --- |
| `$asin(0)` | `0` |
| `$asin(1)` | Approximately `1.5707963267948966` (pi/2 radians) |
| `$asin(-1)` | Approximately `-1.5707963267948966` (-pi/2 radians) |
| `$asin()` | Empty string |

The approximate examples describe the numeric value; the returned string may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `asin` and implements `function_asin` through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX arc sine specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/asin.html) defines radians, the domain, and library error reporting.
