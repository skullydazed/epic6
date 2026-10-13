# tdiff2

## Synopsis

`$tdiff2(<seconds>)`

## Technical

Formats an elapsed duration using abbreviated units: `d`, `h`, `m`, and `s`. Components are separated by spaces, with no space between a number and its unit. Zero components are omitted, except that a zero duration returns `0s`. There is no trailing space.

The first function word is parsed as a signed integer using base-zero conversion. Decimal input normally uses base ten, a leading zero selects octal, and `0x` selects hexadecimal. Conversion stops at the first unrecognized character: `90.5` becomes 90 seconds, while `1e3` becomes one second. Nonnumeric input becomes zero. Additional words are ignored; missing input returns the empty string. There is no JSON form.

The duration is decomposed into 86400-second days, 24-hour remainders, and 60-minute/second remainders. Negative input gives separately negative components, such as `-1h -2s`, rather than one leading minus sign. No singular/plural distinction is made.

## Practical

Use this function for a compact idle time or duration since an event. It does not subtract timestamps itself. [`tdiff`](tdiff.md) provides full unit names and supports fractional seconds and JSON input; the two functions have separate parsers and different zero behavior.

## Returns and restrictions

A human-readable duration string, or empty for missing input. Fractional text is not preserved or formatted.

The parsed value uses `intmax_t` and is assigned to the platform's `time_t` without a range check. Parsing overflow is not reported; the library returns its signed integer boundary value. On platforms where `time_t` is narrower, out-of-range conversion can change the value. Components are printed after conversion to C `long`, so their representable range also depends on the platform. There is no fixed 32-bit seconds limit in this implementation; keep inputs within the platform's signed integer and component ranges. No options, settings, or server connection are required.

## Examples

| Expression | Result |
| --- | --- |
| `$tdiff2(248576)` | `2d 21h 2m 56s` |
| `$tdiff2(-248576)` | `-2d -21h -2m -56s` |
| `$tdiff2(3602)` | `1h 2s` |
| `$tdiff2(90.5)` | `1m 30s` |
| `$tdiff2(010)` | `8s` |
| `$tdiff2(0)` | `0s` |

## History

First appeared in ircII+CS.

## Source

Handler and formatting: [`source/functions.c`](../../source/functions.c:4764). Integer parsing: [`include/functions.h`](../../include/functions.h:25).
