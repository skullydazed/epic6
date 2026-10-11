# floor function

## Synopsis

```text
$floor(number)
```

## Technical

`floor()` calculates the largest integral value less than or equal to `number`, using the C library's `floor()` operation. It rounds toward negative infinity: `3.2` becomes `3`, while `-3.2` becomes `-4`. An already integral value remains unchanged. See [`ceil()`](ceil.md) for rounding toward positive infinity.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply. The first argument word is converted with `atof()`, accepting decimal fractions and scientific notation; additional words are ignored. Nonnumeric input converts to zero, and a numeric prefix can be accepted even when followed by nonnumeric text. Missing input returns the empty string.

## Returns

The result is integral in value but is calculated as a C `double` and returned as a string using `%.50g`, not converted to an integer type. Ordinary whole-number results have no decimal point; very large values may use scientific notation. The [`floating_point_math`](../settings/floating_point_math.md) and [`floating_point_precision`](../settings/floating_point_precision.md) settings do not affect this function.

The result retains floating-point precision limits, and may exceed the range of EPIC's integer arithmetic. Rounding happens after conversion to `double`, so digits already lost during conversion cannot be recovered. The library preserves infinities and returns NaN for NaN input.

The shared math wrapper checks `errno` after the operation: a reported domain error returns `DOM`, a reported range error returns `RANGE`, and another reported error returns the empty string. The standard `floor()` operation defines no such errors for ordinary inputs. The wrapper clears `errno` after argument conversion and does not validate conversion errors or check floating-point exception flags.

## Examples

| Expression | Return value |
| --- | --- |
| `$floor(3.141593)` | `3` |
| `$floor(1.570796)` | `1` |
| `$floor(0)` | `0` |
| `$floor(-3.2)` | `-4` |
| `$floor(4)` | `4` |
| `$floor()` | Empty string |

## Source

[source/functions.c](../../source/functions.c) registers `floor` and implements `function_floor` through the shared math-function and error-return macros. [include/functions.h](../../include/functions.h) defines argument conversion and formatting. The [C/POSIX flooring specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/floor.html) defines rounding and floating-point results.
