# writeb

Decode CTCP-quoted data and write its bytes without appending a newline.

## Synopsis

```irc
$writeb(<handle> <encoded text>)
$writeb(w<window refnum> <encoded text>)
$writeb(w-1 <encoded text>)
```

## Technical

The data must be in EPIC's CTCP-encoded representation. Use [xform](xform.md) with the `ctcp` transform, or [chrq](chrq.md) for byte values, to construct it. The writer decodes it before writing, retaining the decoded byte length so embedded NUL bytes can be written. This does not send a CTCP message or require an IRC connection.

Unlike [write](write.md), no newline is added. For incremental text output, include any required newline in the encoded data. Ordinary text without CTCP quoting characters passes through, but literal quoting characters must be encoded to preserve them. Decoding is permissive: unknown quoting sequences discard the quoting character and retain the following byte; malformed input is not reliably rejected.

A numeric handle selects an EPIC file opened through [open](open.md), rather than an operating-system file descriptor. `w` or `W` followed by a number selects an already open window logfile. `w0` uses the current window, and `w-1` uses the global logfile. `l` or `L` followed by a number is accepted syntactically, but general `/log` targets are unimplemented and return `-1`.

The handle must contain decimal digits, optionally preceded by a minus sign. Window names, plus signs, and hexadecimal prefixes are not accepted. Its parsing follows `/xdebug dword`; the entire remaining text is encoded data, without another dequoting pass. Character-set conversion, logging rewrite, and logging mangling are bypassed.

Each write flushes the stream immediately, without guaranteeing durable storage. The function does not open files, enable logging, or display the data.

## Returns

The number of decoded bytes written, which can be smaller than the requested byte count for a partial write. It does not include any automatically added newline. Invalid or closed handles, unavailable logfiles, streams without an ordinary file pointer, decoding failure, or flush failure return `-1`. Missing or invalid handle syntax returns an empty string.

Empty encoded data is treated as a failed transform: it prints diagnostics and returns `-1`, rather than succeeding with zero bytes. A short write is returned as a count; the function does not separately check the stream error indicator after `fwrite`.

## Examples

Assuming handle 7 is writable and the indicated logfiles are open:

```irc
$writeb(7 blah blah)        /* Writes nine bytes without a newline. */
$writeb(7 $chrq(65 0 66))  /* Writes three bytes: A, NUL, B. */
$writeb(foo bar)           /* Empty result; no write. */
$writeb(w0 this is a test) /* Write to the current window's logfile. */
$writeb(w-1 this is a test) /* Write to the global logfile. */
```

## Source

Registered and implemented as `function_writeb` in [source/functions.c](../../source/functions.c). Writing and logfile selection are in [source/files.c](../../source/files.c); `transform_string_dyn` and the CTCP decoder are in [source/ircaux.c](../../source/ircaux.c).
