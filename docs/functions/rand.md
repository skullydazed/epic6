# rand function

## Synopsis

`$rand(<maximum>)`

`$rand({"max":<maximum>})`

## Technical

For a positive integer maximum, returns a random integer from zero inclusive to the maximum exclusive. It obtains a 64-bit value from OpenSSL's random generator and applies modulo `maximum`; this reduction can introduce distribution bias when the maximum does not divide the source range evenly.

A maximum of zero disables the modulo reduction. The raw value is formatted through EPIC's signed integer return conversion, so values outside the signed range can appear negative; this is not an unsigned-long result as older interfaces described it. The random helper retries a generated zero value, but returns zero if random generation fails. That failure is not reported separately and is indistinguishable from a valid zero result with a positive bound.

Missing ordinary input or a negative maximum returns the empty string. Only the first ordinary integer argument is used; extra text is ignored. Input beginning with `{` selects the JSON form with integer field `max`. A missing JSON field leaves the initial negative value and returns empty; malformed JSON produces a parser diagnostic and returns empty. Supply an integer rather than relying on conversion of other input types.

## Practical

Use a positive bound to choose an index or a value within a fixed range. For a six-sided die, add one to `rand(6)`. The generator is supplied by OpenSSL; there is no source-selection option in this function. [$srand()](srand.md) currently returns empty without seeding or resetting it.

## Returns

A decimal integer string, or the empty string for missing or negative input. No IRC connection is required. OpenSSL-compatible crypto support is required by the current build configuration.

## Examples

| Expression | Result |
| --- | --- |
| `$rand(50)` | An integer from `0` through `49`. |
| `$rand({"max":6})` | An integer from `0` through `5`. |
| `$rand(1)` | `0`. |
| `$rand(0)` | An unreduced random value, possibly formatted as a negative integer. |
| `$rand(-1)` or `$rand()` | Empty string. |

## History

First appeared in ircII.

## Source

Registration, `function_rand()`, JSON parsing, and `function_srand()`: [source/functions.c](../../source/functions.c). `random_number()` and `intmaxtoa()`: [source/ircaux.c](../../source/ircaux.c). Signed return conversion: [include/functions.h](../../include/functions.h). Crypto dependency: [configure.ac](../../configure.ac).
