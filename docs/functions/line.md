# $line()

## Synopsis

`$line(<line> [<window>] [-level] [-time] ...)`

## Technical

Retrieves a logical entry from a window's lastlog. Positions are one-based, counting entries marked visible from newest to oldest: `1` is the newest. Entries from other windows and entries excluded from that window's lastlog do not count. A logical entry can occupy several displayed screen lines.

The line number is required and uses base-detecting integer conversion, accepting decimal, hexadecimal, and leading-zero octal. Nonnumeric text converts to zero. A number less than `1` or larger than the window's lastlog size returns the empty string.

The window defaults to the current window. It can be selected by name, UUID, or reference number; `0` selects the current window. Invalid window descriptions return the empty string. When several window operands are supplied, the last one wins.

`-level` and `-time` are exact case-insensitive options and may appear among the window operands. Any other operand is treated as a window description, including an unrecognized option. Repeated options have no additional effect.

The stored text is returned with its formatting bytes. `-level` appends its canonical output-level name; `-time` appends its creation timestamp as integer Unix time in seconds. If both are selected, level comes before time regardless of option order. Fields are separated by spaces and are not quoted, so the result is not a self-describing record when the text itself contains spaces.

## Practical

Use this to inspect stored text, extract URLs, or implement searches and transformations beyond the [lastlog command](../commands/lastlog.md). The [lastlog function](lastlog.md) returns positions suitable for this function when the same window's lastlog has not changed between calls. Positions shift as entries are added, removed, or moved; they are not persistent identifiers. See [lastlogctl](lastlogctl.md) for reference-based access.

## Returns

The stored text, optionally followed by level and timestamp. Missing line input, an invalid window, or an out-of-range position returns the empty string. An empty stored text also produces an empty result when no metadata is requested. The function does not display or alter the entry.

## Examples

```text
$line(1)
$line(1 0 -level -time)
$line(3 chat)
```

These retrieve the newest entry from the current window, that entry with metadata, and the third-newest visible lastlog entry from the window named `chat`, respectively. The window must exist and the requested position must be present.

## Source

Registered in [source/functions.c](../../source/functions.c) and implemented as `function_line` in [source/lastlog.c](../../source/lastlog.c), beside entry creation and size accounting. Window lookup is in [source/window.c](../../source/window.c), and level naming is in [source/levels.c](../../source/levels.c).
