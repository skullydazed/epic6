# yn

Return the Bessel function of the second kind for an integer order.

## Synopsis

```irc
$yn(<level> <number>)
```

## Technical

Returns the Bessel function of the second kind of order `level`, evaluated at `number`, using the platform math library's `yn(level, number)`. Both arguments are required; omitting either returns an empty string. Additional arguments are ignored. For the first-kind function, see [jn](jn.md).

The order uses base-zero integer conversion and is stored in a C `int`. The number uses `atof`, accepting decimal fractions and scientific notation. Nonnumeric text converts to zero, and numeric prefixes can be accepted despite trailing text. Keep the order within C `int` range. Negative orders are passed through to the library; there is no EPIC-side domain validation.

The ordinary real-valued domain is positive `number`. Zero is a pole and negative arguments cause a domain error. Error reporting, overflow, underflow, and special-value handling depend on the platform library; see the [Linux yn manual](https://man7.org/linux/man-pages/man3/y0.3.html). EPIC does not independently reject these inputs.

Results use C `double` and are formatted with `%.50g`, rather than being limited to six decimal places. The extra printed digits do not imply 50 digits of accuracy. The `floating_point_math` and `floating_point_precision` settings do not control this calculation or formatting.

## Returns

The calculated value when `errno` is zero, `DOM` when it is `EDOM`, `RANGE` when it is `ERANGE`, or an empty string for another error. The wrapper clears `errno` before argument conversion, so conversion errors can affect the final result as well as library calculation errors.

EPIC checks `errno`, rather than floating-point exception flags. If the library reports a special value without setting `errno`, that value is formatted instead of producing an error word. The function is registered unconditionally and requires the platform math library's `yn` implementation.

## Examples

| Expression | Result |
| --- | --- |
| `$yn(0 1)` | Approximately `0.08825696421567696` |
| `$yn(1 1)` | Approximately `-0.7812128213002887` |
| `$yn(1)` | Empty string |
| `$yn(0 -1)` | `DOM` when the library reports `EDOM` |
| `$yn(0 0)` | `RANGE` when the library reports the pole with `ERANGE` |

The approximate examples describe numeric values; the returned strings may contain more digits.

## Source

Registration, `MATH_FUNCTION2`, and `MATH_RETVAL` are in [source/functions.c](../../source/functions.c). Argument conversion and floating-point return formatting are in [include/functions.h](../../include/functions.h).
