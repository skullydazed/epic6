# $stime()

## Synopsis

`$stime(<epoch seconds>)`

`$stime({"time":<epoch seconds>})`

## Technical

Converts an integer count of seconds since the Unix epoch into a human-readable date and time. It uses the client's local timezone and the active locale's `%c` date/time format. The input represents an epoch instant, but the returned text is not necessarily UTC.

The ordinary form consumes one integer argument; additional words are ignored. Numeric prefixes and decimal, hexadecimal, or leading-zero octal forms are accepted by normal integer conversion. Nonnumeric input becomes zero. Missing ordinary input returns empty.

Input beginning immediately with `{` selects the JSON form with integer field `time`. The field defaults internally to `-1` when missing; malformed input can print a parser diagnostic. A final value of `-1` returns empty in either form. Other negative timestamps can describe instants before the epoch if supported by the platform.

The implementation uses `localtime_r()` and `strftime(..., "%c", ...)` through `my_ctime()`, rather than directly calling `ctime()` and trimming its newline. It does not append a newline itself or change the system clock.

## Practical

Use this to display the result of [$time()](time.md) in a readable form. It is a simpler formatting interface than [$strftime()](strftime.md), which allows a chosen format. For predictable machine-readable output, choose an explicit format rather than relying on locale-dependent `%c` text.

## Returns

The formatted local date/time string. `-1`, missing input, or JSON input that leaves the timestamp at `-1` returns empty.

A timestamp outside the helper's supported `time_t` range, or one that cannot be converted by `localtime_r()`, produces `<seconds> <invalid>` rather than empty. Formatting uses a fixed 50-byte buffer and does not check the return value of `strftime()`; an oversized locale format has no reliable error result. Timestamp ranges and formatting depend on the platform. There are no options or IRC connection requirements.

## Examples

`$stime($time())` returns the current date/time in the client's local timezone and locale.

`$stime(0)` formats the Unix epoch instant; the displayed date can be December 31, 1969 in timezones west of UTC.

`$stime({"time":0})` formats the same instant. `$stime(-1)` and `$stime()` return empty.

## History

First appeared in ircII.

Registration and `function_stime()`: [source/functions.c](../../source/functions.c). `my_ctime()` conversion and formatting: [source/ircaux.c](../../source/ircaux.c). Ordinary integer parsing: [include/functions.h](../../include/functions.h).
