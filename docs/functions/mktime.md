# $mktime()

## Synopsis

`$mktime(<year> <month> <day> <hour> <minute> <second> [<dst>])`

## Description

Converts the supplied local calendar date and time to Unix epoch seconds using the system `mktime()` function. Six fields are required; daylight-saving information is optional. Additional arguments after the seventh are ignored.

| Argument | Meaning |
| --- | --- |
| `year` | Years since 1900: use `126` for 2026 |
| `month` | Month starting at zero: January is `0`, December is `11` |
| `day` | Day of the month, normally 1–31 |
| `hour` | Hour, normally 0–23 |
| `minute` | Minute, normally 0–59 |
| `second` | Second, normally 0–59 |
| `dst` | Positive: initially assume daylight-saving time; `0`: initially assume standard time; negative or omitted: let the system determine it |

Fields use EPIC's integer argument parsing with base detection: decimal, hexadecimal, and leading-zero octal are accepted. Nonnumeric text converts to zero. Use decimal values without leading zeroes for ordinary dates. Each value is stored as a C `int`; keep inputs within that range.

There is no default date or time for the first six fields. There is also no separate timezone argument. The system interprets the fields in the process's local timezone, including its `TZ` environment setting when applicable. It can normalize out-of-range calendar fields rather than rejecting them; for example, month `12` denotes January of the following year. Daylight-saving transitions and dates beyond the platform's time range are handled by the system library. See the [POSIX mktime specification](https://pubs.opengroup.org/onlinepubs/009695399/functions/mktime.html).

## Returns

An integer number of seconds since 1970-01-01 00:00:00 UTC. Returns `-1` when fewer than six fields are supplied or when the system conversion fails. The system's `-1` error value can also coincide with a valid instant just before the epoch; this wrapper provides no separate error detail.

The function does not change the system clock, return the normalized calendar fields, or query an IRC server. [$strftime()](strftime.md) formats timestamps for display.

## Examples

```text
$mktime(126 9 12 12 0 0)     local noon on October 12, 2026; DST determined automatically
$mktime(126 0 1 0 0 0 0)    local midnight on January 1, 2026; initially assume standard time
$mktime(126 0 1)             returns -1 (missing fields)
```

If the process timezone is UTC, `$mktime(70 0 1 0 0 0 0)` returns `0`. In another timezone, the same local calendar fields generally produce a different timestamp.

## History

This function first appeared in EPIC5 0.0.3.

Registration and `function_mktime()`: [source/functions.c](../../source/functions.c). Integer argument conversion: [include/functions.h](../../include/functions.h).
