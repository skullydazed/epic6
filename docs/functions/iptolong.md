# iptolong function

## Synopsis

`$iptolong(<dotted-quad>)`

## Technical

Converts an IPv4 address `A.B.C.D`, with each component from `0` through `255`, to its unsigned decimal integer representation. The result is `A*16777216 + B*65536 + C*256 + D`. The implementation converts the stored network-order address with `ntohl()`, so the decimal number does not depend on the machine's byte order.

The address is the first function argument word; additional arguments are ignored. Quote-aware parsing follows `/xdebug dword`. Numeric parsing uses the system `getaddrinfo()` interface with `AI_NUMERICHOST`, so hostnames are not resolved through DNS. Use ordinary dotted-quad notation rather than relying on platform-specific abbreviated numeric forms.

## Practical

This reverses [$longtoip()](longtoip.md) for IPv4 addresses. It is useful when a script or external protocol needs an IPv4 address represented as one decimal number. It performs no connection or IRC request.

## Returns

An unsigned decimal integer string from `0` through `4294967295`. Missing input returns empty. A numeric-address parse failure returns empty and produces a system-error diagnostic. Zero is a valid result, not a failure code.

## Examples

| Expression | Result |
| --- | --- |
| `$iptolong(127.8.4.3)` | `2131231747` |
| `$iptolong(127.0.0.1)` | `2130706433` |
| `$iptolong(0.0.0.0)` | `0` |
| `$iptolong(255.255.255.255)` | `4294967295` |
| `$iptolong()` | Empty string |

## Restrictions

Use IPv4 only. The helper accepts numeric addresses with an unspecified family, and the wrapper does not verify that the returned address is IPv4 before reading it as one. IPv6 input may therefore return a meaningless number rather than being rejected; it is not an IPv6-to-integer conversion.

## Source

Registration, address argument parsing, IPv4-layout access, `ntohl()`, and unsigned formatting: [source/functions.c](../../source/functions.c). Numeric-only address parsing and diagnostics: [source/network.c](../../source/network.c).
