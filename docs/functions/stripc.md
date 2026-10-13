# stripc

## Synopsis

`$stripc(<text>)`

## Technical

Returns `<text>` with parsed IRC color sequences removed, including control-C (`^C`) colors and extended control-X (`^X`) colors such as 256-color and RGB forms. It consumes their recognized color parameters as well as the introducer.

Equivalent to [`$stripcrap(COLOR <text>)`](stripcrap.md). This is a compatibility interface; prefer `stripcrap` when selecting which formatting to remove. The entire argument is text, including spaces and literal quotation marks.

## Returns

The processed text without recognized IRC color sequences. Empty input returns the empty string. There are no options or server requirements.

Other IRC attributes, including bold, reverse, underline, italic, and reset, remain effective, although their control representation may be rewritten. ANSI escape sequences remain unchanged: stripping ANSI color requires normalization as well as color stripping, for example `$stripcrap(ANSI,COLOR <text>)`.

The shared text processor also expands tabs to spaces at eight-column stops, drops Unicode C1 controls, and renders carriage returns as a reverse-formatted `m`. Newlines, bells, and other ordinary control characters remain. Invalid UTF-8 bytes may be skipped. This is not a general plain-text conversion, and malformed color parameters can remain as ordinary text.

## Examples

```irc
$stripc($*)                       Remove IRC colors from the current arguments.
$stripcrap(COLOR $*)               Equivalent processing through stripcrap.
$stripcrap(ANSI,COLOR $*)           Normalize ANSI sequences and remove colors.
$stripc(plain text)                Returns plain text.
```

For ANSI normalization without color removal, see [`stripansicodes`](stripansicodes.md).

## Source

Handler: [`source/functions.c`](../../source/functions.c:6946). Color parsing and text processing: [`source/screen.c`](../../source/screen.c:1822). The `COLOR` option maps to `STRIP_COLOR` in [`parse_mangle`](../../source/screen.c:4719).
