# set floating_point_precision

## Synopsis

```text
/set floating_point_precision [<number>]
```

Default: `16`. With no number, [set](../commands/set.md) displays the current value. The value must be a non-negative integer; negative or nonnumeric values are rejected.

## Description

Controls the significant-digit precision used by the shared `ftoa` number-to-string conversion helper when [floating_point_math](floating_point_math.md) is on. The helper uses C's `%g` formatting: the number is rounded for text output, trailing fractional zeros are omitted, and scientific notation may be selected. This is a significant-digit count, not a fixed number of decimal places.

Zero uses `%g`'s effective precision of one significant digit. It is not equivalent to turning floating-point math off. With that other setting off, `ftoa` ignores this precision, truncates the fractional part toward zero, and formats the result without a fractional part.

## Current scope

In the current source tree, numeric values imported by [json_explode](../functions/json_explode.md) use this helper when creating script variables. Changing the setting affects later conversions; it does not recalculate or recover precision in strings already stored.

This is not a universal precision control for each arithmetic operation. The expression evaluator formats floating tokens through a separate `%Lf` and `canon_number` path, normally starting with six fractional digits. Math functions using the `RETURN_FLOAT` macro also use their own formatting rather than `ftoa`. Increasing this setting does not increase the underlying floating-point arithmetic accuracy or change those separate conversion paths.

There is no setting-specific upper bound beyond ordinary integer handling, but formatted output is limited by the helper's buffer. Use a reasonable precision; arbitrarily large values do not create additional meaningful digits. The setting has no immediate callback and sends no IRC request.

## Example

Use six significant digits for later `ftoa`-based conversions:

```text
/set floating_point_math on
/set floating_point_precision 6
```

For that conversion path, `123.456789` becomes `123.457`; setting precision to zero instead yields one significant digit, typically `1e+02`. These examples describe conversion formatting, not an expression-evaluator precision guarantee.

Restore the default with `/set floating_point_precision 16`.

## History

This setting first appeared in EPIC4-1.1.9.

## Source references

- [source/ircaux.c](../../source/ircaux.c): `ftoa` uses `%.*g` with this precision when floating-point math is enabled.
- [source/functions.c](../../source/functions.c): `explode_json_object` converts JSON numbers through `ftoa`.
- [source/expr2.c](../../source/expr2.c) and [source/expr.c](../../source/expr.c): separate expression-token formatting and canonicalization.
- [include/functions.h](../../include/functions.h): `RETURN_FLOAT` has independent formatting.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): integer registration, validation, and default 16.
