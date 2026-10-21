# Rules about number arguments

When a built-in function expects a number, the following conventions are common, but the individual function's documented behavior takes precedence. Command arguments, expression operands, and JSON forms can use different parsing rules. “Expects a number” does not imply strict validation of the entire supplied text.

## Argument conversion

| Case | Common built-in-function behavior |
| --- | --- |
| Missing required argument | Return an empty string when the argument-reading helper reaches the missing value. This is not a universal guarantee that an entire function or enclosing expression has had no earlier side effects. |
| Integer argument | Take the next function word and convert with `strtoimax(text, NULL, 0)`. |
| Floating-point argument | Take the next function word and convert with `atof(text)`. |

The integer conversion uses base zero: `10` is decimal ten, `010` is octal eight, and `0x10` is hexadecimal sixteen. An initial sign is accepted by that conversion. It does not check the end pointer or `errno`, so trailing text can be ignored and invalid or out-of-range input is not reliably rejected by the common helper. A function can subsequently reject a negative value or enforce its own range.

Floating-point conversion likewise does not establish that the whole token was a valid number. Do not treat a zero result as proof that the supplied text represented zero. Whether `nan`, infinity, or extreme inputs are useful depends on the function and platform library.

For example, in expression mode these calls use the common floating argument conversion:

```irc
@ result = abs(-12.5)
@ result = abs(12.5extra)
```

The first returns `12.5`; the second can also convert the numeric prefix to `12.5`. Supply well-formed input rather than relying on prefix acceptance.

## Return formatting

Integer results using the standard return helper are formatted as signed decimal integers through `intmaxtoa()`. A nonempty `0` is a valid result, not an empty result. Some functions pass unsigned values through this signed interface, so consult their range descriptions.

Floating results using the usual helper are formatted as a C `double` with `%.50g`: up to 50 significant digits, with fixed or exponential notation chosen by the formatter. This does not create 50 digits of numerical precision. Some functions deliberately use another format, such as two fixed decimal places.

These return helpers do not use the expression parser's fixed-decimal formatting path. In particular, the `floating_point_math` and `floating_point_precision` settings are not universal controls over all built-in function results. See [math operators](math_operators.md) for expression conversion.

## Math-library errors

The shared wrappers for functions such as `abs`, `sin`, and `log` clear `errno`, call the math library, then return:

| Library status | Result |
| --- | --- |
| No error | The floating result formatted with `%.50g`. A non-finite result can be rendered as text such as `nan` or `inf` by the platform formatter. |
| `EDOM` | `DOM`, indicating a domain error. |
| `ERANGE` | `RANGE`, indicating a range error. |
| Another error | Empty string. |

Whether a particular operation sets these errors depends on the library and input. These marker strings are not numeric success values, and this wrapper behavior is not a blanket rule for expression operators or every numeric function. Check the called function rather than assuming that all invalid arithmetic produces `DOM`.

## Validation and limits

Use the function's documented accepted range and error result. EPIC's `is_number()` helper is a separate decimal-text test, not a model of everything accepted by base-zero conversion; a command that validates with it first can reject a spelling that a function's conversion would accept. Other interfaces may parse decimal only, use unsigned conversion, or accept JSON numbers through another parser.

Numeric text is still subject to ordinary [function argument expansion](expando_substitution.md) and word extraction. A function argument is text, not an automatically evaluated arithmetic expression: write `abs(${2 - 5})` when calculation is intended. Numeric parsing itself does not require a server connection or script package, but the operation receiving the number can have additional effects.

## Source references

- `include/functions.h`: `GET_INT_ARG`, `GET_FLOAT_ARG`, `RETURN_INT`, `RETURN_FLOAT`, and `RETURN_FLOAT2`.
- `source/functions.c`: shared math wrappers and function-specific validation.
- `source/ircaux.c`: `intmaxtoa()`, `is_number()`, and numeric helpers.
- `source/expr2.c` and `source/expr.c`: the separate expression conversion and formatting paths.
