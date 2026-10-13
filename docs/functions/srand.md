# $srand()

## Synopsis

`$srand()`

## Technical

A compatibility function that always returns the empty string and performs no operation. All arguments are ignored by its C handler. Normal EPIC argument expansion still occurs before the handler runs.

It does not seed a generator, read the system time, or reset a random sequence. [$rand()](rand.md) obtains values through EPIC's OpenSSL-backed random helper; calling `$srand()` does not affect that helper.

## Practical

Existing scripts can retain calls to this function, but no initialization call is needed before `$rand()`. Supplying a numeric seed does not make subsequent random results reproducible.

## Returns

Always the empty string. There are no options, diagnostics, state changes, or IRC connection requirements.

## Examples

`$srand()` and `$srand(12345)` both return empty and leave random generation unchanged.

## History

First appeared in ircII as a random-generator seeding interface. The current built-in is a no-op.

Registration and `function_srand()`: [source/functions.c](../../source/functions.c). Current `random_number()` implementation: [source/ircaux.c](../../source/ircaux.c).
