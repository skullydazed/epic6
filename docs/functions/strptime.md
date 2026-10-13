# strptime

## Synopsis

`$strptime("<format>" <string>)`

## Technical

Parses a human-readable date/time using the system's `strptime` format directives, then calls `mktime` to convert the parsed fields to a Unix timestamp. The first argument is always a quoted-word argument, so quote a format containing spaces. The remaining argument is the input string; literal quotation marks around that string are not removed.

Missing format input or a library parsing failure returns the empty string. Successful parsing need only match a prefix: trailing input is ignored rather than rejected. There is no JSON form or option argument.

## Practical

Consult your system's `strptime(3)` manual for supported directives. Many resemble [`strftime`](strftime.md) directives, but parsing support is platform-dependent. Avoid system-specific formats in portable scripts.

`mktime` interprets fields using the local timezone and daylight-saving state. A timezone or UTC offset parsed by the library is not separately applied by EPIC; do not assume that `%z` provides portable UTC conversion. Locale-sensitive names and available directives also depend on the system library. See the [strptime manual](https://man7.org/linux/man-pages/man3/strptime.3.html) and [mktime specification](https://man7.org/linux/man-pages/man3/mktime.3p.html).

## Returns

A decimal Unix timestamp, in seconds since the epoch, after successful parsing. `mktime` failure returns `-1`; EPIC does not check `errno` or distinguish this from a valid timestamp whose value is `-1`. Supported date ranges depend on the platform's `time_t`.

## Restrictions

The current implementation does not initialize the date/time structure before parsing. Fields omitted by the format can therefore contain indeterminate values, including the daylight-saving flag `tm_isdst` when the system parser leaves it untouched. Incomplete dates do not reliably return `-1`, and even a full date/time format cannot guarantee a repeatable result. Do not rely on this function for dependable timestamp conversion until that initialization defect is fixed.

## Examples

```irc
$strptime("%Y-%m-%d %T" 1985-10-26 01:24:00)
$strptime("%a %b %d %T %Y" Sun Jun 14 18:27:10 2009)
```

These show accepted format syntax; their numeric results depend on the system timezone and the initialization limitation above. A date-only format such as `%Y-%m-%d` or a time-only format such as `%H:%M:%S` leaves additional fields unspecified.

## History

First appeared in EPIC5-0.3.6.

## Source

Registration and handler: [`source/functions.c`](../../source/functions.c:8435). Quoted argument parsing: [`include/functions.h`](../../include/functions.h:45).
