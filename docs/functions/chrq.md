# chrq function

## Synopsis

```text
$chrq(byte-or-codepoint ...)
```

## Technical

`chrq()` builds a byte string from a word list, then returns its CTCP-encoded representation. It is similar to [`chr()`](chr.md), but uses an explicit byte count when encoding, so embedded NUL bytes and bytes after them are preserved in encoded form. The result can be stored in an EPIC string without embedded NULs.

Ordinary numeric words are converted with base-detected `strtol()`, then cast directly to `char`. Decimal, hexadecimal (`0x`), and leading-zero octal notation are accepted, as are numeric prefixes with trailing nonnumeric text. Unlike `chr()`, this branch does not skip negative or greater-than-255 values; their narrowed result depends on the platform's C conversion rules. Use values from 0 to 255 for portable byte input. A nonnumeric ordinary word converts to zero.

Words beginning with the exact uppercase prefix `U+` are parsed as hexadecimal Unicode code points and converted to UTF-8. A word with no hexadecimal digit after that prefix is skipped. Numeric prefixes with trailing nonnumeric text are accepted. `U+0000` explicitly adds a NUL byte, unlike that form in `chr()`. Use valid Unicode scalar values; invalid or out-of-range code points are not comprehensively validated by the conversion helper.

Word parsing honors [`xdebug dword`](../commands/xdebug.md). This function returns encoded text; it does not display it or send an IRC message.

## CTCP encoding

| Input byte | Encoded characters |
| --- | --- |
| NUL (`0`) | `\0` |
| CTCP delimiter (`1`) | `\a` |
| Newline (`10`) | `\n` |
| Carriage return (`13`) | `\r` |
| Backslash (`92`) | `\\` |

Other bytes are copied unchanged, including high-bit bytes and most control characters. Encoding does not guarantee valid UTF-8 or acceptance by a particular IRC server; normal message length, character, and policy restrictions still apply. It does not add the outer CTCP delimiter framing used by the [`ctcp` command](../commands/ctcp.md).

## Restrictions

[`asciiq()`](asciiq.md) decodes CTCP text into a numeric byte list, but its conversion stops at the first decoded NUL. It is therefore not a general binary inverse for `chrq()`: `$asciiq($chrq(65 0 66))` returns `65`. For input without NUL, it can recover the encoded bytes.

Empty input, or input consisting entirely of skipped `U+` words, returns the empty string and can produce the transform helper's failure diagnostic because zero decoded bytes are treated as a failed transformation. Other transform failures also return empty.

## Examples

`$chrq(3 0 10 65)` returns Control-C followed by the literal characters `\0\nA`. The Control-C byte is not caret notation in the returned string.

| Expression | Return value |
| --- | --- |
| `$chrq(65 0 66)` | Literal `A\0B` |
| `$chrq(65 U+0000 66)` | Literal `A\0B` |
| `$chrq(U+00E9)` | UTF-8 `é`, unchanged by CTCP encoding |
| `$asciiq($chrq(65 10 92))` | `65 10 92` |

## Source

[source/functions.c](../../source/functions.c) registers `chrq` and implements `function_chrq()`. [source/ircaux.c](../../source/ircaux.c) implements numeric conversion, UTF-8 encoding, `transform_string_dyn()`, and the CTCP encoder. [include/words.h](../../include/words.h) defines word parsing.
