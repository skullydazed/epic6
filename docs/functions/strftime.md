# $strftime()

## Synopsis

`$strftime(<format>)`

`$strftime(<epoch seconds> <format>)`

## Technical

Formats the specified epoch instant using the operating system's `strftime()` date/time format. Without a recognized leading timestamp, it uses the current time. Conversion uses the client's local timezone and the active locale, not necessarily UTC.

Timestamp detection examines only the first input byte: an ASCII digit selects integer parsing of the first function word. Otherwise the entire input is treated as the format. Thus a format beginning with a digit can accidentally be consumed as a timestamp, while a negative timestamp, a leading `+`, or leading whitespace is treated as format text rather than a timestamp.

Recognized timestamps use normal base-detecting integer conversion: hexadecimal and leading-zero octal are accepted, and trailing nonnumeric characters in the timestamp word can be ignored. After that word, the entire remaining text is the format. Without a timestamp, the entire input is the format. Do not quote formats just to group spaces: quotation marks are literal format characters.

There is no JSON argument form. Input beginning with `{` is ordinary format text and uses the current time. Normal EPIC expression expansion can supply the format and timestamp before this built-in runs.

## Practical

Use this for timestamps with a chosen appearance, such as `%Y/%m/%d`. Consult the platform's `strftime(3)` documentation for its supported conversion specifiers; system-specific extensions reduce portability. Locale-dependent conversions such as `%B`, `%x`, and `%X` can vary with the active locale. [$stime()](stime.md) supplies a simpler locale-dependent date/time format, and [$time()](time.md) supplies epoch seconds.

## Returns

The formatted string. Missing or empty format, a timestamp that fails the `time_t` range check, failure of `localtime_r()`, or a zero result from `strftime()` returns empty. The output buffer is 128 bytes, so successful output must fit in 127 bytes plus its terminator. An oversized result returns empty rather than being returned truncated.

Invalid or unsupported format syntax follows the platform library's behavior; it is not guaranteed to return empty. An empty result also does not distinguish failure from a format that legitimately produces no characters. There are no options or IRC connection requirements.

## Examples

| Expression | Example result |
| --- | --- |
| `$strftime(%d %B %Y)` | `13 December 1996` |
| `$strftime(%x at %X)` | `12/13/96 at 16:45:42` |
| `$strftime(%m/%d/%y)` | `07/16/01` |
| `$strftime(%Y/%m/%d)` | `2001/07/16` |

These examples depend on the actual date, timezone, and locale. `$strftime(0 %Y/%m/%d)` formats the epoch in the local timezone; a timezone west of UTC can display `1969/12/31`.

`$strftime(-1 %Y)` treats `-1 ` as literal format text and formats the current year; it does not select the second before the epoch.

## History

First appeared in ircII-2.8.1 and was first included in EPIC3pre8.

Registration and `function_strftime()`: [source/functions.c](../../source/functions.c). Timestamp validation: [source/ircaux.c](../../source/ircaux.c), `is_valid_time_t()`. Integer argument parsing: [include/functions.h](../../include/functions.h).
