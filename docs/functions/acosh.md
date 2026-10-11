# acosh function

## Synopsis

```text
$acosh(number)
```

## Technical

`acosh()` calls the C library's `acosh()` function to return the inverse hyperbolic cosine of `number`. For finite inputs, `number` must be at least 1; the result is nonnegative. [`cosh()`](cosh.md) performs the corresponding hyperbolic cosine operation.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Missing input returns the empty string. Nonnumeric input converts to zero, which is outside this function's domain. A numeric prefix can be accepted even when followed by nonnumeric text.

## Returns and restrictions

The calculation uses a C `double`, and the result is formatted with `%.50g`. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting. Returned rounding digits do not imply 50 digits of accuracy.

An argument below 1 causes a library domain error. EPIC returns `DOM` when the library reports that error through `errno`; if the platform reports it only through a floating-point exception, the result can instead be a NaN string. The shared wrapper returns `RANGE` for a reported range error and the empty string for another reported error. It checks `errno`, not floating-point exception flags, so callers should validate the input rather than assume one particular error string.

This built-in has no optional configure-time gate; it relies on the platform math library.

## Examples

| Expression | Result |
| --- | --- |
| `$acosh(1)` | `0` |
| `$acosh(2)` | Approximately `1.3169578969248166` |
| `$acosh()` | Empty string |

The approximate example describes the numeric value; the returned string may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `acosh` and implements `function_acosh` through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX inverse hyperbolic cosine specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/acosh.html) defines the domain and library error reporting.
