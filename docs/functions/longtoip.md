# $longtoip()

## Synopsis

`$longtoip(<integer>)`

## Technical

Converts an integer IPv4 address representation into dotted-quad text. The argument is required; omitting it returns the empty string. Only the first function argument is used, and additional arguments are ignored.

The input is parsed with `strtoul` in base `10`, then reduced to a 32-bit address. It represents `a.b.c.d` as `(a << 24) + (b << 16) + (c << 8) + d`, independent of the machine's byte order. Byte-order conversion is applied when building the IPv4 socket address; callers should supply the numeric value, not a machine-specific memory representation.

Conversion is permissive and is not range-validated. Numeric prefixes are accepted despite trailing text, nonnumeric text becomes zero, and hexadecimal syntax such as `0x7f000001` is not recognized as hexadecimal. Values above the 32-bit address range discard higher bits when representable as an unsigned long. Negative text follows unsigned conversion, and overflow of unsigned long is not checked; use decimal values from `0` through `4294967295` for predictable input.

The address is formatted through numeric-host lookup, without DNS resolution. IPv6 is not supported. A formatting failure returns the empty string and can produce a system-error diagnostic.

## Practical

This reverses [iptolong](iptolong.md) for IPv4 addresses. To look up the resulting address's hostname, use [iptoname](iptoname.md). The conversion does not open a connection or verify that the address is reachable.

## Returns

The dotted-quad IPv4 address, or the empty string for missing input or a formatting failure. Invalid numeric text commonly returns `0.0.0.0`, rather than an error.

## Examples

| Expression | Result |
| --- | --- |
| `$longtoip(2131231747)` | `127.8.4.3` |
| `$longtoip(${(127 << 24) + (8 << 16) + (4 << 8) + 3})` | `127.8.4.3` |
| `$longtoip(0)` | `0.0.0.0` |
| `$longtoip(4294967295)` | `255.255.255.255` |
| `$longtoip(hello)` | `0.0.0.0` |
| `$longtoip()` | Empty string |

## Source

Registered and implemented as `function_longtoip` in [source/functions.c](../../source/functions.c). Numeric address formatting (`ssu_to_paddr`) is in [source/network.c](../../source/network.c).
