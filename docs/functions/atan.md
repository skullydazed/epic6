# atan function

## Synopsis

```text
$atan(number)
$atan(y x)
```

## Technical

With one argument, `atan()` returns the principal arc tangent of `number` in radians, using the C library's `atan()` function. Every finite real number is in its domain; the result is between -pi/2 and pi/2. Positive and negative infinity produce positive and negative pi/2, respectively. The input is a tangent value, not an angle in degrees.

With two arguments, this same EPIC function calls the library's `atan2(y, x)`. It determines the angle of the point `(x, y)`, using both signs to select a quadrant; the result is between -pi and pi radians. The first argument is `y`, and the second is `x`. This form handles a zero `x` without first dividing by it. There is no separate C built-in named `atan2`.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. Arguments are converted with `atof()`, accepting decimal fractions and scientific notation. Nonnumeric arguments convert to zero, and numeric prefixes can be accepted even when followed by nonnumeric text. Missing input returns the empty string. Additional words after the second argument are ignored. See [`tan()`](tan.md) for tangent calculation.

## Returns

The calculation uses C `double` values, and the result string is formatted with `%.50g`, rather than being limited to six decimal places. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not change this calculation or formatting. Additional displayed rounding digits do not imply 50 digits of accuracy.

The handler clears `errno` before converting its arguments, then checks it after the library calculation. It returns `DOM` for a reported domain error, `RANGE` for a reported range error, and the empty string for another reported error. Very small subnormal inputs to the one-argument form, or an underflowing result in the two-argument form, can cause platform-dependent range errors. Only `errno` is checked; floating-point exception flags are not. A conversion error can also affect this handler's error result because it does not clear `errno` between conversion and calculation.

## Examples

| Expression | Result |
| --- | --- |
| `$atan(0)` | `0` |
| `$atan(1)` | Approximately `0.7853981633974483` (pi/4 radians) |
| `$atan(-1)` | Approximately `-0.7853981633974483` |
| `$atan(1 0)` | Approximately `1.5707963267948966` (positive y-axis) |
| `$atan(1 -1)` | Approximately `2.356194490192345` (second quadrant) |
| `$atan()` | Empty string |

The approximate examples describe the numeric value; the returned string may contain more digits.

## Source

[source/functions.c](../../source/functions.c) registers `atan` and implements both forms in `function_atan()`, using the shared error-return macro. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The C/POSIX specifications for [atan](https://pubs.opengroup.org/onlinepubs/9799919799/functions/atan.html) and [atan2](https://pubs.opengroup.org/onlinepubs/9799919799/functions/atan2.html) define library ranges and error reporting.
