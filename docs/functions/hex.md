# hex function

## Synopsis

`$hex(<digits> <value>)`

`$hex({"digits": <digits>, "value": <value>})`

## Technical

Formats a nonnegative integer as uppercase hexadecimal text with exactly `digits` output characters. The digit count comes first and is required; there is no default width. Shorter values are padded on the left with zeroes. If the hexadecimal representation is longer than the requested width, the output is truncated on the right, retaining its leading digits rather than its least significant digits.

The positional form consumes two function argument words and ignores additional words. Both numbers use base-detecting integer conversion: decimal, leading-zero octal, and `0x` hexadecimal are accepted. Conversion can stop at trailing nonnumeric text; a wholly nonnumeric word converts to zero. Quote-aware positional parsing follows `/xdebug dword`.

If input starts with `{`, it is parsed as JSON with `digits` and `value` fields. Numeric fields convert to integers; numeric strings use base-detecting conversion. Missing fields become zero. Other JSON fields are ignored. Supply valid integer values rather than relying on coercion of other JSON types.

## Returns

An uppercase hexadecimal string without a `0x` prefix. Missing positional arguments, zero digits, or a negative value returns empty. Invalid JSON prints an error and normally returns empty because the digit count remains zero. Numeric conversion errors do not have a separate error result.

## Restrictions

Use a positive, modest digit count and a value within the platform's signed `intmax_t` range. Negative widths are not rejected safely, and excessive widths can cause allocation failure or terminate the client. Width is not a request to mask or reduce the value modulo a power of 16.

## Examples

| Expression | Result |
| --- | --- |
| `$hex(4 255)` | `00FF` |
| `$hex(2 0)` | `00` |
| `$hex(2 4095)` | `FF` (truncated from `FFF`) |
| `$hex(2 0x1234)` | `12` |
| `$hex({"digits": 4, "value": 255})` | `00FF` |
| `$hex(0 255)` | Empty string |
| `$hex(4 -1)` | Empty string |

## Source

Registration, positional and JSON argument handling, allocation, and bounded hexadecimal formatting: [source/functions.c](../../source/functions.c). Integer argument conversion: [include/functions.h](../../include/functions.h). Allocation limits: [source/ircaux.c](../../source/ircaux.c).
