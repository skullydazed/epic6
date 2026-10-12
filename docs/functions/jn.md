# $jn()

## Synopsis

`$jn(<level> <number>)`

## Technical

Returns the Bessel function of the first kind of integer order `<level>` evaluated at `<number>`, using the C library's `jn(level, number)`. Both arguments are required; omitting either returns the empty string. Additional arguments are ignored.

The order uses base-detecting integer conversion, accepting decimal, hexadecimal, and leading-zero octal, then is stored as a C `int`. Keep it within that type's range. The number uses `atof`, accepting decimal fractions and scientific notation. Nonnumeric arguments convert to zero, and numeric prefixes can be accepted despite trailing text. Negative orders and arguments are passed to the library without an EPIC restriction.

The calculation uses a C `double`. Results are formatted with `%.50g`, not limited to six decimal places; extra digits do not imply 50 digits of accuracy. The [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md) settings do not change this calculation or formatting.

## Returns

The calculated Bessel value when `errno` is zero, `DOM` for `EDOM`, `RANGE` for `ERANGE`, or the empty string for another reported error. Unlike the unary math wrapper, this wrapper clears `errno` before argument conversion, so conversion errors can also affect the result.

EPIC checks `errno`, not floating-point exception flags. Large arguments or underflow can produce a library range error; the exact error reporting is platform-dependent. See the [POSIX jn specification](https://pubs.opengroup.org/onlinepubs/9799919799/functions/j0.html). This function is registered unconditionally and requires the platform math library's `jn` implementation.

## Examples

| Expression | Result |
| --- | --- |
| `$jn(0 0)` | `1` |
| `$jn(1 0)` | `0` |
| `$jn(0 1)` | Approximately `0.7651976865579666` |
| `$jn(1)` | Empty string |

The approximate example describes the numeric value; the returned string may contain more digits. For the Bessel function of the second kind, see [yn](yn.md).

## Source

Registration, `MATH_FUNCTION2`, and `MATH_RETVAL` are in [source/functions.c](../../source/functions.c). Argument conversion and floating-point return formatting are in [include/functions.h](../../include/functions.h).
