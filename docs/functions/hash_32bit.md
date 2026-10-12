# hash_32bit function

## Synopsis

`$hash_32bit(<word> [<length>])`

## Technical

Returns a small numeric hash of the first `length` bytes in `word`, or the entire word if shorter. Both arguments are double-quote-aware words regardless of `/xdebug dword`; quote text containing spaces. Missing length, zero, a negative length, or a length greater than 64 selects the default of 20 bytes. Length uses base-detecting integer conversion; nonnumeric text converts to zero and therefore selects the default.

The algorithm folds each byte to lowercase with C's `tolower()`, then combines two shifting accumulators and masks the result. ASCII uppercase and lowercase spellings therefore hash identically. Length counts bytes, not Unicode characters. The final masks limit the returned value to `0` through `65535`, despite the function's name.

Hashing is lossy: different words can produce the same result, and the original text cannot be recovered from the hash. For reversible encoding instead, see [$encode()](encode.md). The result is deterministic within the client, but the algorithm may change in future versions; do not save hashes expecting permanent compatibility.

Normally extra arguments are ignored. An empty or missing word instead falls back to the remaining input after the length argument has been consumed. Thus `$hash_32bit()` and `$hash_32bit("" 20)` return `0`, while `$hash_32bit("" 20 hello)` hashes `hello`. Avoid the empty-word form when supplying extra text.

## Practical

Use this function to turn text containing characters inconvenient for alias names into a number that can form part of an alias name. Keep the original text separately if you must detect collisions. This hash is not suitable for cryptographic verification or password storage.

## Returns

A decimal integer string in the range `0` through `65535`. There is no separate error code or diagnostic.

## Examples

| Expression | Result |
| --- | --- |
| `$hash_32bit("hello there")` | `51439` |
| `$hash_32bit("HELLO THERE")` | `51439` |
| `$hash_32bit(hello 1)` | `832` |
| `$hash_32bit(hello 0)` | `25851` (default length 20) |
| `$hash_32bit()` | `0` |

## Restrictions

Use ASCII input when portable results matter. The implementation processes plain C `char` bytes and passes them directly to `tolower()`; bytes above ASCII can depend on locale and signed-character behavior, and are not handled as Unicode code points. Different platforms or locales need not produce compatible results for such input.

## Source

Registration, quoted-word parsing, default length, hash calculation, and final masks: [source/functions.c](../../source/functions.c). Word parsing and integer conversion: [include/words.h](../../include/words.h) and [source/ircaux.c](../../source/ircaux.c). Integer-string return formatting: [include/functions.h](../../include/functions.h).
