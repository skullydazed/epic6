# tobase

## Synopsis

`$tobase(<base> <number>)`

## Technical

Converts an integer to a string in `<base>`, which must be from `2` through `36`. Digits above nine are uppercase letters `A` through `Z`. No base prefix or padding is added. A leading minus sign is retained for ordinary negative input; zero returns `0`.

Both arguments use function-word parsing and base-zero integer conversion: decimal is usual, leading zero selects octal, and `0x` selects hexadecimal. Thus `<number>` is not limited to decimal input. Conversion accepts a numeric prefix before trailing text; nonnumeric input becomes zero. Additional words are ignored.

## Practical

Use this function to convert integers to binary, octal, or hexadecimal. [`strtol`](strtol.md) converts text in a specified base to a decimal integer.

## Returns

The converted string, or empty if an argument is missing or the parsed base is outside `2` through `36`. An unconvertible number returns `0`, which is indistinguishable from a valid zero.

The base is parsed as `intmax_t` then narrowed to C `int` before validation; avoid base arguments outside that type's range. Parsing overflow is not reported. A minus sign is handled specially only when it is the first character of the remaining number argument; use ordinary unquoted signed numeric text.

## Restrictions

Although the input is stored in `intmax_t`, digit generation uses floating-point `pow` to determine lengths and divisors, then converts those powers back to integers. Large values can be rounded incorrectly; near integer limits, power conversion can exceed the signed range and make behavior undefined. This is not a reliable full-width integer converter. Use modest values whose powers and divisions are accurately representable, rather than assuming every `intmax_t` value is supported.

There are no options, settings, or server requirements.

## Examples

```irc
$tobase(16 180099407)    Returns ABC194F.
$tobase(2 10)            Returns 1010.
$tobase(16 -255)         Returns -FF.
$tobase(10 010)          Returns 8.
$tobase(16 0)            Returns 0.
$tobase(1 10)            Returns the empty string.
```

## History

First appeared in EPIC5-0.0.2.

## Source

Handler: [`source/functions.c`](../../source/functions.c:8098). Integer argument parsing: [`include/functions.h`](../../include/functions.h:25).
