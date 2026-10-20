# utime script

## Synopsis

```text
/load utime
$utime_add(<sec1> <usec1> <sec2> <usec2>)
$utime_sub(<newer_sec> <newer_usec> <older_sec> <older_usec>)
$time_since(<seconds> <microseconds>)
```

## Description

The built-in `$utime()` function returns a two-word timestamp: seconds followed by the fractional microseconds within that second. This script defines three functions for arithmetic on those values. It does not replace the built-in timestamp function.

`utime_add` adds two timestamp or interval pairs. For ordinary normalized inputs it can calculate a future timestamp by adding a duration to a timestamp. `utime_sub` subtracts the second pair from the first; put the newer, larger timestamp first to obtain elapsed time. `time_since` obtains the current built-in timestamp and subtracts the supplied pair.

Each helper returns **two space-separated words**, seconds and a six-digit microsecond field. The result is not a decimal number and is not a scalar count of microseconds. For a valid nonnegative elapsed pair, total microseconds can be calculated as `seconds * 1000000 + microseconds`.

## Limits and edge cases

Supply numeric seconds and normalized microseconds from `0` through `999999`, with exactly four words for addition/subtraction or two for `time_since`. The helpers do not validate missing, malformed, out-of-range, or extra arguments and do not provide a separate error result. They only perform one carry or borrow adjustment; these are not general normalizers for arbitrary pairs.

There is an addition boundary bug: the script carries only when the microsecond sum is **greater than** `1000000`, not equal to it. For example, `$utime_add(10 500000 0 500000)` returns `10 000000` instead of the expected `11 000000`. Do not rely on this helper when that boundary must be handled correctly. The return formatting takes the last six characters of a zero-prefixed microsecond value, which can also conceal invalid larger inputs.

Subtraction borrows one second when its microsecond difference is negative. Reversed timestamps are not rejected and can produce a negative seconds field; the script does not format a signed human-readable duration. `time_since` uses the real-time clock, so clock adjustments can make its result negative or otherwise unsuitable for monotonic elapsed-time measurement. Microsecond formatting does not guarantee microsecond clock accuracy.

These functions return text without displaying it, scheduling a timer, changing their input variables, accessing a file, or sending an IRC request. There are no settings, flags, helper-script dependencies, external programs, or optional platform requirements. Loading defines global aliases of these names.

## Examples

```text
/load utime
/echo $utime_add(100 900000 0 200000)
/echo $utime_sub(102 100000 100 900000)
```

The results are `101 100000` and `1 200000`, respectively. To measure from a saved timestamp in a script:

```text
@ started = utime();
# Later:
echo $time_since($started);
```

The saved timestamp must be expanded as its two words, not wrapped into one quoted argument.

## History

This script first appeared in EPIC4pre0.042. The retained source credits hop in 1997 and describes Kanan's request for timestamp arithmetic helpers.

## Source references

- [script/utime](../../script/utime): arithmetic, one-step carry/borrow, and six-character formatting.
- [source/functions.c](../../source/functions.c): `function_utime` returns seconds and microseconds; `right` implements the result-field truncation.
- [source/ircaux.c](../../source/ircaux.c): `get_time` reads the real-time clock.
