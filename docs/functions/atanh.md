# atanh function

## Synopsis

```text
$atanh(number)
```

## Technical

`atanh()` calls the C library's `atanh()` function to return the inverse hyperbolic tangent of `number`. A finite result requires a number strictly between -1 and 1. [`tanh()`](tanh.md) performs the corresponding hyperbolic tangent operation.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Missing input returns the empty string. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text.

## Returns

The calculation uses a C `double`, and the result string is formatted with `%.50g`, rather than being limited to six decimal places. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change this calculation or formatting. Additional displayed rounding digits do not imply 50 digits of accuracy.

The library reports a domain error for values below -1 or above 1, including infinities. Exactly -1 or 1 causes a pole error instead. EPIC returns `DOM` for a reported `EDOM` error and `RANGE` for a reported `ERANGE` error, including a pole error. A platform that reports errors only through floating-point exception flags can instead produce a NaN string for an out-of-domain input or a signed infinity string at a pole. Validate the range rather than relying on one particular error string.

Very small subnormal inputs can also cause a platform-dependent range error. The shared wrapper returns the empty string for any other reported error. It checks only `errno`, not floating-point exception flags, and resets `errno` after argument conversion, so conversion errors are not validated by this wrapper.

## Examples

| Expression | Result |
| --- | --- |
| `$atanh(0)` | `0` |
| `$atanh(0.5)` | Approximately `0.5493061443340548` |
| `$atanh(-0.5)` | Approximately `-0.5493061443340548` |
| `$atanh()` | Empty string |

The approximate examples describe the numeric value; the returned string may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `atanh` and implements `function_atanh` through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX inverse hyperbolic tangent specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/atanh.html) defines the domain, poles, and library error reporting.
