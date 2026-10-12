# $metric_time()

## Synopsis

`$metric_time()`

## Return Value

Returns the current system wall-clock time as two space-separated numeric fields:

| Field | Meaning |
| --- | --- |
| First | Whole days since the Unix epoch, 1970-01-01 00:00:00 UTC |
| Second | Millidays elapsed within the current UTC day, including a fractional part |

For ordinary dates after the epoch, the second field runs from `0` up to, but not including, `1000`. One milliday is 86.4 seconds. It is the remainder within the day, not the total number of millidays since the epoch. Combine the fields as `days + millidays / 1000` to obtain fractional days since the epoch.

The milliday field is printed with six digits after the decimal point and a minimum width of nine characters, so small values can have extra leading space. This formatting is fixed and does not depend on `floating_point_math` or `floating_point_precision`. Any arguments are ignored.

## Practical

Use this representation when you want a decimal division of a day. It follows the system's real-time clock, rather than a monotonic elapsed-time clock, and does not use your local timezone.

## Restrictions

The conversion discards fractions smaller than one millisecond before calculating millidays. Six printed decimal places do not imply six decimal places of clock accuracy. System clock adjustments can move the result backward or forward; do not assume successive calls are strictly increasing.

## Examples

`$metric_time()` might return `12854 58.797431`. This represents day 12854 since the epoch and about 58.797431 millidays into that day. At UTC noon, the second field is `500.000000`.

## History

This function first appeared in EPIC4 1.1.13.

Registration and `function_metric_time()`: [source/functions.c](../../source/functions.c). `get_metric_time()`, `timespec_to_metric()`, and the `CLOCK_REALTIME` clock helper: [source/ircaux.c](../../source/ircaux.c).
