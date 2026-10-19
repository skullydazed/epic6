# set random_source

## Removed setting

The former setting used this syntax:

```text
/set random_source [<0|1|2|3>]
```

It selected a random-number source used by [rand](../functions/rand.md) and [randread](../functions/randread.md). The choices represented different tradeoffs in predictability, distribution, and repeatability:

| Former value | Source |
| --- | --- |
| `0` | `/dev/urandom`, with a traditional PRNG fallback when unavailable. |
| `1` | Traditional ircII pseudo-random generator. |
| `2` | Time-of-day calls. |
| `3` | `arc4random`. |

EPIC6 does not register `random_source` as a setting and does not select among these implementations. There is no supported selector value, default, or fallback policy under this name. Historical instructions to choose a repeatable generator or another source no longer apply.

Without script-provided handling of unknown settings, `/set random_source 0` reports `No such variable "RANDOM_SOURCE"`. Remove obsolete assignments from startup scripts. A script accepting the same name defines its own behavior rather than changing the C generator.

## Current random numbers

The shared `random_number` helper uses the OpenSSL-compatible `RAND_bytes` API to obtain eight bytes and returns a 64-bit value. Successful zero results are retried; an API failure returns zero. Both `rand` and `randread` call that helper. This path is fixed rather than selected through a setting, and EPIC's build requires its OpenSSL-compatible library support.

The [srand](../functions/srand.md) function does not seed a repeatable sequence; its current implementation returns an empty string. Neither it nor this removed setting provides the former repeatability option.

For ordinary bounded random values, use the current function directly:

```text
/echo $rand(10)
```

There is no preceding source-selection assignment. See the function documentation for its range and argument behavior.

## History

`set random_source` first appeared in EPIC4pre2.001-NR13. Its former `arc4random` option appeared in EPIC4-1.1.8.

## Source references

- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/ircaux.c](../../source/ircaux.c): `random_number` uses `RAND_bytes` without selecting among former sources.
- [source/functions.c](../../source/functions.c): `rand`, `randread`, and the no-op `srand` implementation.
- [configure.ac](../../configure.ac): required OpenSSL-compatible library support.
