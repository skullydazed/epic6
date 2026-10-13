# stripansi

## Synopsis

`$stripansi(<text>)`

## Technical

Returns `<text>` with ASCII characters 14 through 31 inclusive, except 15 (`^O`) and 22 (`^V`), converted to printable ASCII characters by adding 64 to their values. All other bytes are unchanged. The entire argument is treated as text; spaces and literal quotation marks are retained.

For example, ESC (27) becomes `[`, italic (16) becomes `P`, and underline (31) becomes `_`. This disables ESC sequences by replacing their ESC byte, but leaves the rest of each sequence in the result: ESC followed by `[31m` becomes `[[31m`.

## Practical

This function is maintained for backward compatibility. Prefer [`stripansicodes`](stripansicodes.md) for ANSI escape sequence normalization.

It does not remove all control characters or all IRC formatting. Tabs, line breaks, IRC bold, color, reset, and reverse controls remain unchanged. It does not interpret cursor movement sequences or convert their effects into a screen layout.

## Returns

The input string with the specified control bytes converted to printable characters. Empty input returns the empty string. There are no options, settings, or server requirements.

## Examples

```irc
$stripansi($*)                 Replace ESC bytes in the current arguments.
$stripansi(plain text)         Returns plain text.
```

## Source

Registration and implementation: [`source/functions.c`](../../source/functions.c:4831). IRC formatting control definitions: [`include/irc.h`](../../include/irc.h:47).
