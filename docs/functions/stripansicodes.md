# stripansicodes

## Synopsis

`$stripansicodes(<text>)`

## Technical

Equivalent to [`$stripcrap(ANSI <text>)`](stripcrap.md): normalizes the entire argument as text, converting recognized ANSI display attributes to IRC formatting controls. Existing IRC colors and formatting are also normalized rather than removed. Literal quotation marks are part of the text.

Recognized ANSI attributes include bold, underline, reverse, blink, and supported foreground/background colors. Unsupported attributes are ignored. This is a compatibility interface; prefer `stripcrap` when choosing which formatting to remove.

## Returns

The normalized text, which can still contain IRC formatting and control characters. Empty input returns the empty string. There are no options or server requirements.

Normalization expands tabs to spaces at eight-column stops, retains newlines and bells, removes carriage returns and Unicode C1 controls, and converts other ordinary control characters to printable equivalents wrapped in reverse formatting. Invalid UTF-8 bytes may be skipped.

Explicit ANSI cursor-right sequences such as ESC followed by `[3C` produce three EPIC non-destructive space controls; counts are capped at 256. Other cursor operations do not reconstruct a terminal screen. The output is not guaranteed to be plain text or byte-for-byte equivalent to the input with escape sequences deleted.

## Restrictions

Escape parsing recognizes a limited set of sequence forms. Unsupported or malformed sequences can leave part of their suffix as ordinary text; do not assume that every terminal sequence is removed as a complete unit. The function returns a string and does not execute terminal commands or change the input editor.

## Examples

```irc
$stripansicodes($*)            Normalize ANSI formatting in the current arguments.
$stripcrap(ANSI $*)            Equivalent normalization through stripcrap.
$stripansicodes(plain text)    Returns plain text.
```

Unlike [`stripansi`](stripansi.md), this function interprets recognized escape sequences instead of replacing each ESC byte with a printable character.

## Source

Handler: [`source/functions.c`](../../source/functions.c:6763). Normalization and escape parsing: [`source/screen.c`](../../source/screen.c:1822), [`read_esc_seq`](../../source/screen.c:1369). The `ANSI` flag maps to `NORMALIZE` in [`parse_mangle`](../../source/screen.c:4707).
