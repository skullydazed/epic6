# trunc

## Synopsis

`$trunc(<precision> <float>)`

## Technical

Formats and rounds a floating-point number to the requested precision. Despite the name, it does not simply discard digits toward zero; rounding follows the system's floating-point formatting behavior.

For nonnegative `<precision>`, the result uses fixed decimal notation with exactly that many digits after the decimal point, adding trailing zeros when needed. Precision zero omits the decimal point. Leading formatting spaces are removed.

A negative precision requests an initial scientific-format rounding to `-precision` significant digits. That intermediate value is then converted to C `float`, printed with six decimal places, and stripped of trailing fractional zeros and a trailing decimal point. This second conversion can lose precision or round small values to zero; it is not a general accurate significant-digit formatter.

Precision uses base-zero integer parsing, while the value uses `atof` on the next function word. Nonnumeric precision becomes zero, nonnumeric value becomes zero, and numeric prefixes before trailing text can be accepted. Additional words are ignored.

## Practical

Use this function for decimal output such as displaying two fractional places. The [floating_point_math](../settings/floating_point_math.md) setting may affect expressions that calculate its input, but this function itself always converts to C `double` and does not consult that setting or [floating_point_precision](../settings/floating_point_precision.md).

## Returns and restrictions

The formatted number, or empty if either required argument is missing. NaN and infinity are passed through the formatting/conversion path rather than mapped to `DOM` or `RANGE`. There is no explicit conversion or formatting error check.

Output uses a fixed 2048-byte buffer and can be cut short for very large precision or magnitude. Precision is narrowed to C `int` without range checking; extreme negative values can also overflow the precision calculation. Use modest precision values. There are no options or server requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$trunc(0 3.141592654)` | `3` |
| `$trunc(2 3.141592654)` | `3.14` |
| `$trunc(5 3.54)` | `3.54000` |
| `$trunc(2 3.146)` | `3.15` under ordinary rounding |
| `$trunc(-2 1234)` | `1200` |
| `$trunc(-2 0.00000001)` | `0` after six-decimal formatting |

## Source

Handler: [`source/functions.c`](../../source/functions.c:4710). Argument parsing: [`include/functions.h`](../../include/functions.h:25). Buffer size: [`include/irc.h`](../../include/irc.h:80).
