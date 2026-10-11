# asinh function

## Synopsis

```text
$asinh(number)
```

## Technical

`asinh()` calls the C library's `asinh()` function to return the inverse hyperbolic sine of `number`. Every finite real number is in its domain, including negative numbers and zero. [`sinh()`](sinh.md) performs the corresponding hyperbolic sine operation.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Missing input returns the empty string. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text.

## Returns and restrictions

The calculation uses a C `double`, and results are formatted with `%.50g`. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting. Additional displayed rounding digits do not imply 50 digits of accuracy.

Very small subnormal inputs can cause a platform-dependent range error. EPIC returns `RANGE` when the library reports that error through `errno`. The shared wrapper also returns `DOM` for a reported domain error and the empty string for another reported error, though ordinary finite inputs do not have a domain restriction. It checks `errno`, not floating-point exception flags, and resets `errno` after argument conversion; it does not validate conversion errors.

This built-in has no optional configure-time gate and relies on the platform math library.

## Examples

| Expression | Result |
| --- | --- |
| `$asinh(0)` | `0` |
| `$asinh(1)` | Approximately `0.881373587019543` |
| `$asinh(-1)` | Approximately `-0.881373587019543` |
| `$asinh()` | Empty string |

The approximate examples describe the numeric value; the returned string may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `asinh` and implements `function_asinh` through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX inverse hyperbolic sine specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/asinh.html) defines library results and error reporting.
