# tanh

## Synopsis

`$tanh(<number>)`

## Technical

Returns the hyperbolic tangent of `<number>`, mathematically `sinh(x) / cosh(x)`, using the system C library and a C `double`. It is defined for all real inputs and approaches `-1` and `1` toward negative and positive infinity. Floating-point rounding can produce exactly those endpoint values for large finite inputs. [`atanh`](atanh.md) is the inverse hyperbolic tangent.

The first function word is converted with `atof`, accepting fractions, scientific notation, and numeric prefixes before trailing text. Nonnumeric input becomes zero. Additional words are ignored; missing input returns the empty string.

## Returns

The hyperbolic tangent, formatted with `%.50g`, rather than limited to six decimal places. Extra printed digits do not imply 50 digits of accuracy. The [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting.

Positive infinity returns `1`, negative infinity returns `-1`, and signed zero retains its sign. NaN input ordinarily returns NaN. These special values follow the [C library hyperbolic tangent interface](https://man7.org/linux/man-pages/man3/tanh.3.html).

The shared wrapper clears `errno` after argument conversion, then returns `DOM` for a reported domain error, `RANGE` for a reported range error, or empty for another reported error. It checks `errno`, not floating-point exception flags; these tokens are not guaranteed for errors reported only through exception flags.

## Examples

| Expression | Result |
| --- | --- |
| `$tanh(0)` | `0` |
| `$tanh(1)` | Approximately `0.7615941559557649` |
| `$tanh(-1)` | Approximately `-0.7615941559557649` |
| `$tanh()` | Empty string |

Approximate examples describe values; returned strings can contain more digits. There are no options or server requirements.

## Source

Registration and math wrapper: [`source/functions.c`](../../source/functions.c:7722). Argument conversion and return formatting: [`include/functions.h`](../../include/functions.h:33).
