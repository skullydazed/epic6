# strtol

## Synopsis

`$strtol(<base> <number>)`

## Technical

Converts `<number>` from the specified base to a decimal integer. `<base>` must be `0` or an integer from `2` through `36`. Missing arguments or an unsupported base return the empty string. Both arguments use function-word parsing; additional arguments are ignored.

Base `0` detects the number's base: a leading `0x` or `0X` selects hexadecimal, a leading `0` selects octal, and other numbers use decimal. Some system libraries also recognize binary prefixes; use explicit base `2` with unprefixed binary digits for portability. Bases above ten use case-insensitive letters `a` through `z` as additional digits. An optional leading sign is accepted.

Conversion stops at the first character invalid for the base and returns the converted prefix. It returns `0` if there is no convertible prefix; it does not validate the entire word. For example, `12junk` in base ten returns `12`, while `junk12` returns `0`.

## Practical

Use this function to convert binary, octal, or hexadecimal numbers to decimal. [`tobase`](tobase.md) converts a decimal integer to another base.

## Returns

The converted value as a signed decimal integer. Conversion uses the system's `intmax_t`, rather than necessarily the narrower `long` type. Overflow returns `INTMAX_MAX` or `INTMAX_MIN` according to the sign; EPIC does not check `errno`, so these results cannot distinguish overflow from a valid boundary value. See the [strtoimax specification](https://man7.org/linux/man-pages/man3/strtoimax.3p.html).

The base itself is parsed using base-zero integer conversion before validation: `0x10` selects base 16, and nonnumeric base text parses as zero. That result is narrowed to C `int`; avoid out-of-range base arguments, whose narrowing is implementation-dependent. There are no settings or server requirements.

## Examples

```irc
$strtol(16 ABC194F)    Returns 180099407.
$strtol(2 1010)        Returns 10.
$strtol(0 010)         Returns 8.
$strtol(10 12junk)     Returns 12.
$strtol(10 junk12)     Returns 0.
$strtol(1 10)         Returns the empty string.
```

## History

First appeared in EPIC5-0.0.2.

## Source

Handler: [`source/functions.c`](../../source/functions.c:8072). Argument conversion and return formatting: [`include/functions.h`](../../include/functions.h:25).
