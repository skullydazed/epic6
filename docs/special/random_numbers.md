# About pseudo-random numbers

Random-number interfaces serve different purposes. Three useful properties to distinguish are unpredictability, distribution across possible values, and repeatability. None means that a short sequence must look evenly mixed or that a value cannot repeat.

## Entropy and unpredictability

Unpredictability concerns how difficult it is to infer future results from what has already been observed. Entropy describes uncertainty in the source or state; it is not simply the apparent variety of a short output sequence. The generator's implementation and state management matter, not merely whether the initial seed is secret.

For intuition, selecting a card and shuffling again before the next selection permits repeats. Seeing one card does not mean the remaining choices must avoid it. EPIC's bounded random function makes independent selections rather than maintaining a deck of unused results.

## Coverage and distribution

A uniform distribution gives each possible value the same probability on each draw. It does not promise that every value occurs equally often over a small number of draws. Selecting cards without replacement is a different operation: it guarantees no duplicates until the deck is exhausted. If a script needs that behavior, it must keep track of the remaining choices itself.

Reducing a random integer with remainder arithmetic can introduce a small distribution bias when the source range is not an exact multiple of the requested bound. This matters when choosing an interface for a task requiring exact statistical properties.

## Repeatability

A deterministic generator can reproduce a sequence when its algorithm, starting state, and calls are identical. This is useful for reproducible simulations, but is different from unpredictable selections. EPIC6 does not expose a seed/reset operation that makes `rand()` replay a chosen sequence.

## What EPIC uses

The current C `random_number()` helper obtains eight bytes through the crypto library's `RAND_bytes()` interface. It returns a 64-bit value and retries if successful generation produced zero. If the library reports failure, it returns zero without a separate error indicator. There is no fallback to a clock-based or classic ircII generator in this helper.

The current build requires OpenSSL-compatible crypto support. Random generation does not require an IRC connection or a script package. Its properties depend on that library and environment; do not infer a particular internal algorithm or state size from the script interface.

## Getting a number

Use [rand](../functions/rand.md) with a positive bound:

```irc
@ index = rand(10)
@ die = rand(6) + 1
```

These produce an index from `0` through `9` and a die result from `1` through `6`. Repeated values are expected and allowed. Positive-bound results are calculated by taking the helper's value modulo the bound, so the distribution is not guaranteed to be exactly uniform.

| Form | Result |
| --- | --- |
| `$rand(n)` with positive integer `n` | Integer from zero inclusive to `n` exclusive. |
| `$rand({"max":6})` | JSON argument form, returning `0` through `5`. |
| `$rand(1)` | Always `0`. |
| `$rand(0)` | Unreduced 64-bit result, formatted through EPIC's signed integer return conversion; it can appear negative. |
| `$rand()` or a negative bound | Empty string. |

Use ordinary valid integer arguments rather than relying on permissive numeric conversion. Malformed JSON can produce a parser diagnostic. A zero returned after crypto-library failure is indistinguishable from a valid bounded zero; `rand()` does not provide a separate success flag. See the function page for argument and error details.

The retained [srand](../functions/srand.md) function returns an empty string without seeding or resetting this generator. For the retired source-selection setting, see [random_source](../disambiguation/set_random_source.md); the historical four-source comparison is not a selection interface for the current helper.

## Choosing values without repeats

To draw without replacement, keep a collection of candidates, choose a valid index using its current count, retrieve that item, and remove it before the next draw. [Karll arrays](karll_arrays.md) provide indexed storage and deletion, but deletion renumbers later items and an empty collection must be handled before calling a bounded selection. This implements the deck metaphor rather than relying on the generator to provide coverage automatically.

## Source references

- `source/ircaux.c`: `random_number()`, `RAND_bytes()` use, retry, and failure behavior.
- `source/functions.c`: `rand()` bounds/JSON handling and inert `srand()`.
- `include/functions.h`: argument parsing and signed integer result conversion.
- `configure.ac`: required crypto-library support.
