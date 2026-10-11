# ceil function

## Synopsis

```text
$ceil(number)
```

## Technical

`ceil()` calculates the smallest integral value greater than or equal to `number`, using the C library's `ceil()` operation. It rounds toward positive infinity: `3.2` becomes `4`, while `-3.2` becomes `-3`. An already integral value remains unchanged. See [`floor()`](floor.md) for rounding toward negative infinity.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text. Missing input returns the empty string.

## Returns

The result is integral in value but is calculated as a C `double` and returned as a string using `%.50g`, not converted to an integer type. Ordinary whole-number results have no decimal point; very large values may use scientific notation. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not affect this function.

The result retains floating-point precision limits, and may exceed the range of EPIC's integer arithmetic. Rounding happens after conversion to `double`, so digits already lost during conversion cannot be recovered. The library preserves infinities and returns NaN for NaN input.

The shared math wrapper checks `errno` after the operation: a reported domain error returns `DOM`, a reported range error returns `RANGE`, and another reported error returns the empty string. The standard `ceil()` operation defines no such errors for ordinary inputs. The wrapper clears `errno` after argument conversion and does not validate conversion errors or check floating-point exception flags.

## Examples

| Expression | Return value |
| --- | --- |
| `$ceil(3.141593)` | `4` |
| `$ceil(1.570796)` | `2` |
| `$ceil(0)` | `0` |
| `$ceil(-3.2)` | `-3` |
| `$ceil(4)` | `4` |
| `$ceil()` | Empty string |

## Source

[source/functions.c](../../source/functions.c) registers `ceil` and implements `function_ceil` through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX ceiling specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/ceil.html) defines rounding and floating-point results.
