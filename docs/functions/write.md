# write

Write text followed by a newline to an EPIC file handle or an open logfile.

## Synopsis

```irc
$write(<handle> [text])
$write(w<window refnum> [text])
$write(w-1 [text])
```

## Technical

A numeric handle refers to an EPIC file opened through [open](open.md), not an operating-system file descriptor. Prefixing a window reference with `w` or `W` selects that window's logfile. `w0` selects the current window's logfile; `w-1` selects the global logfile. The logfile must already be open.

The first argument uses normal function word parsing controlled by `/xdebug dword`. The remaining text is written directly, including spaces, literal quotes, and formatting codes, followed by one newline. Omitted text writes a blank line. Percent signs in the text are literal. No character-set conversion, logging rewrite, or logging mangling is applied. Embedded newlines in the text are retained in addition to the appended newline.

The handle must contain decimal digits, optionally preceded by a minus sign. A plus sign, hexadecimal prefix, or a window name is not accepted. Use [windowctl](windowctl.md) to resolve a window name to its reference first. The parser also accepts `l` or `L` followed by a number, but general `/log` targets are unimplemented in the writer and always return `-1`.

## Practical

Use this function to save configuration or other text to an already open writable file. [Writeb](writeb.md) writes without an appended newline and decodes CTCP-quoted binary data; it is not simply the same text writer with its newline disabled.

Writing flushes the C stream immediately. A successful flush does not establish that data has been committed to durable storage. This function does not open a file, enable logging, or add the line to window output or lastlog.

## Returns

A nonnegative byte count from the text write, including the appended newline. Counts are bytes, not Unicode characters. An invalid or closed handle, absent logfile, stream without a writable ordinary file pointer, or write/flush failure returns a negative result, normally `-1`. Invalid handle syntax or a missing handle returns an empty string, rather than `-1`.

## Examples

Assuming handle 7 is an open writable file and the indicated logfiles are open:

```irc
$write(7 blah blah)        /* Writes blah blah and a newline; returns 10. */
$write(7)                 /* Writes a newline; returns 1. */
$write(foo bar)           /* Empty result; no write. */
$write(w1 this is a test)  /* Write to window 1's logfile. */
$write(w0 this is a test)  /* Write to the current window's logfile. */
$write(w-1 this is a test) /* Write to the global logfile. */
```

## Source

Registered and implemented as `function_write` in [source/functions.c](../../source/functions.c). Handle selection and writing use `target_file_write`, `file_write`, and `lookup_window_logfile` in [source/files.c](../../source/files.c).
