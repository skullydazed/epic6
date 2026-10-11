# abs function

## Syntax

```text
$abs(number)
```

## Technical

`abs()` returns the absolute value of `number` using the C library's `fabs()` operation. It accepts a floating-point number, including a decimal fraction or scientific notation. Only the first argument word is used; additional words are ignored.

The normal [rules about number arguments](../rules_about_number_arguments.md) apply: a missing argument returns the empty string, and the argument is converted with `atof()`. A nonnumeric argument converts to zero; a numeric prefix can be accepted even when followed by nonnumeric text. This function does not validate that the entire argument is a number.

## Returns and restrictions

The result is a string formatted with `%.50g`: whole-number results normally have no decimal point, and large or small values may use scientific notation. The operation always uses a C `double`, regardless of the [`floating_point_math`](../settings/floating_point_math.md) or [`floating_point_precision`](../settings/floating_point_precision.md) settings. Large integers can lose precision, and values outside the platform's floating-point range are not reliable inputs. Displaying up to 50 significant digits does not give the calculation 50 digits of accuracy.

The shared math-function wrapper returns `DOM` for a reported domain error, `RANGE` for a reported range error, and the empty string for another reported error. `fabs()` ordinarily succeeds for finite inputs; these error strings do not provide validation of the original numeric conversion.

## Examples

| Expression | Result |
| --- | --- |
| `$abs(-3)` | `3` |
| `$abs(5)` | `5` |
| `$abs(0)` | `0` |
| `$abs(-2.5)` | `2.5` |
| `$abs()` | Empty string |

## History

The `abs()` function first appeared in EPIC4-1.1.2.

## Source

[source/functions.c](../../source/functions.c) registers `abs` and implements `function_abs` through the shared math-function macros. [include/functions.h](../../include/functions.h) defines floating-point argument conversion and result formatting.
