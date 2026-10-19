# set floating_point_math

## Synopsis

```text
/set floating_point_math [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

Controls whether numeric results converted to text retain a fractional part. When on, floating-point results can be represented with fractions. When off, those conversions discard the fractional part rather than rounding to the nearest integer.

This is not a switch that changes every arithmetic operation from integer to floating point. The expression evaluator already uses floating-point arithmetic for operations such as addition, multiplication, and division, while operations such as remainder and bitwise operations use integers. The setting affects conversion of results into the strings used by scripts, assignments, and output.

An intermediate fractional result can therefore remain available within an expression even when its final textual representation is truncated. Once a result is assigned or otherwise converted to text, discarded fractional information is not recovered by turning the setting on later.

## Formatting and limits

Different numeric conversion paths use different formatting:

- The expression evaluator formats floating results with `%Lf`, normally six digits after the decimal point, then removes trailing fractional zeros when this setting is on. With it off, the decimal point and following text are removed.
- Functions using the shared `ftoa` helper use [floating_point_precision](floating_point_precision.md), default `16`, as significant-digit precision when this setting is on. That path can use scientific notation. With it off, the helper truncates the fraction and formats an integer-looking result.

Thus `floating_point_precision` does not universally set expression-evaluator precision. Floating arithmetic is approximate, and conversion to text can lose precision even with this setting enabled. Functions that explicitly use integer results or their own formatting need not change behavior with this switch.

Changing the setting affects subsequent conversions, has no redraw callback, and sends no IRC request.

## Example

```text
/set floating_point_math on
/echo ${5.0 / 2.0}
/set floating_point_math off
/echo ${5.0 / 2.0}
```

The expression results are displayed as `2.5` and `2`, respectively. Re-enable the setting if later script calculations require fractional results to survive textual conversion.

## Source references

- [source/expr.c](../../source/expr.c): `canon_number` trims fractional zeros or removes the fractional part according to the setting.
- [source/expr2.c](../../source/expr2.c): numeric tokens retain typed values internally; conversion to raw strings uses `%Lf` and `canon_number`; arithmetic includes both floating and integer operators.
- [source/ircaux.c](../../source/ircaux.c): `ftoa` uses significant-digit precision when enabled and truncates otherwise.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration, default off, and default precision 16.
