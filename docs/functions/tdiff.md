# tdiff

## Synopsis

```irc
$tdiff(<seconds>)
$tdiff({"seconds": <number>})
```

## Technical

Formats an elapsed duration in days, hours, minutes, and seconds. It does not subtract timestamps itself. To display elapsed time, calculate the difference first and pass the resulting seconds.

Ordinary input converts the first function word with `atof`; fractions and scientific notation are accepted, numeric prefixes can be accepted before trailing text, and nonnumeric text becomes zero. Extra words are ignored. JSON syntax is selected only when the first input character is `{`; supply a numeric `seconds` field. JSON strings are also converted numerically, booleans become zero or one, and null, arrays, or objects become zero. A missing field or malformed JSON returns empty and can print a diagnostic.

The integral part is truncated toward zero, then decomposed into 86400-second days, 24-hour remainders, and 60-minute/second remainders. Zero components are omitted. A positive component equal to one uses the singular unit; other values use the plural. Negative durations produce separately negative components rather than one leading minus sign.

Fractional seconds are added to the seconds remainder and printed with `%Lg` (six significant digits by default), followed by `seconds`. The original fractional spelling is not retained, and the displayed fraction can be rounded. A trailing space is removed.

## Practical

Use this function for a readable idle time or duration since an event. [`tdiff2`](tdiff2.md) provides abbreviated units, but uses a separate integer parser and does not share this function's fractional or JSON interface.

## Returns and restrictions

A human-readable duration string. Missing input returns empty. A zero duration also returns empty; a whole minute or hour omits the zero seconds component.

NaN, infinity, or a whole-second value outside `intmax_t` or the platform's `time_t` range returns empty. The day count is subsequently stored in C `int`, so large accepted durations with more days than that type can represent have implementation-dependent output. There is no fixed 32-bit seconds limit, but keep durations within representable component ranges. Floating-point input can also lose precision for large values. There are no options, settings, or server requirements.

## Examples

| Expression | Result |
| --- | --- |
| `$tdiff(248576)` | `2 days 21 hours 2 minutes 56 seconds` |
| `$tdiff(-248576)` | `-2 days -21 hours -2 minutes -56 seconds` |
| `$tdiff(3602)` | `1 hour 2 seconds` |
| `$tdiff(60)` | `1 minute` |
| `$tdiff(1.5)` | `1.5 seconds` |
| `$tdiff({"seconds": 90.5})` | `1 minute 30.5 seconds` |
| `$tdiff(0)` | Empty string |

## History

First appeared in ircII-2.2pre5.

## Source

Handler and formatting: [`source/functions.c`](../../source/functions.c:1652). JSON argument conversion: [`parse_kwargs`](../../source/functions.c:874). Range checks: [`source/ircaux.c`](../../source/ircaux.c:7282).
