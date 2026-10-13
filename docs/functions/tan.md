# tan

## Synopsis

`$tan(<number>)`

## Technical

Returns the tangent of the angle given in radians, using the system C library and a C `double`. [`atan`](atan.md) returns a principal inverse angle.

The first function word is converted with `atof`, accepting fractions, scientific notation, and numeric prefixes before trailing text. Nonnumeric input becomes zero. Additional words are ignored; missing input returns the empty string.

## Returns

The tangent, formatted with `%.50g`, rather than limited to six decimal places. Extra printed digits do not imply 50 digits of accuracy. The [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md) settings do not change this function's calculation or formatting.

The wrapper clears `errno` after argument conversion, then returns `DOM` for a library-reported domain error, `RANGE` for a reported range error, or empty for another reported error. It checks `errno`, not floating-point exception flags. Infinite input ordinarily produces a domain error and NaN; EPIC returns `DOM` if reported through `errno`, otherwise it can return a NaN string. NaN input ordinarily returns NaN. See the [C library tangent interface](https://man7.org/linux/man-pages/man3/tan.3.html).

## Restrictions

Tangent has mathematical poles at odd multiples of π/2. A decimal approximation to such an angle is a finite floating-point value, so it can return a large finite result rather than an error or infinity. Accuracy can suffer near these poles or for very large angles; do not use a `DOM` or `RANGE` result as the only test for proximity to a pole.

## Examples

| Expression | Result |
| --- | --- |
| `$tan(0)` | `0` |
| `$tan(1)` | Approximately `1.5574077246549023` |
| `$tan(-1)` | Approximately `-1.5574077246549023` |
| `$tan()` | Empty string |

Approximate examples describe values; the returned strings can contain more digits. There are no options or server requirements.

## Source

Registration and math wrapper: [`source/functions.c`](../../source/functions.c:7722). Argument conversion and return formatting: [`include/functions.h`](../../include/functions.h:33).
