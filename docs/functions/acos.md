# acos function

## Synopsis

```text
$acos(number)
```

## Technical

`acos()` calls the C library's `acos()` function to return the principal arc cosine of `number`. For a number between -1 and 1 inclusive, the result is an angle between zero and pi radians. It does not return degrees. [`cos()`](cos.md) performs the corresponding cosine operation.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; further words are ignored. Missing input returns the empty string. Nonnumeric input converts to zero, and trailing nonnumeric characters do not necessarily prevent a numeric prefix from being used.

## Returns and restrictions

Results use a C `double` and are formatted with `%.50g`. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting. The long result string can expose rounding digits; it does not imply 50 digits of accuracy.

Arguments outside [-1, 1] cause a domain error in the library operation. EPIC returns `DOM` if the library reports that error through `errno`; a platform that reports it only as a floating-point exception can instead produce a NaN result string. The wrapper also returns `RANGE` for a reported range error, or the empty string for another reported error. It checks `errno`, not floating-point exception flags. Validate the argument's range rather than relying on one particular error string.

## Examples

| Expression | Result |
| --- | --- |
| `$acos(1)` | `0` |
| `$acos(0)` | Approximately `1.5707963267948966` (pi/2 radians) |
| `$acos(-1)` | Approximately `3.141592653589793` (pi radians) |
| `$acos()` | Empty string |

The approximate examples describe the numeric value; the returned string may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `acos` and implements `function_acos` through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX arc cosine specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/acos.html) defines radians, the valid range, and library error reporting.
