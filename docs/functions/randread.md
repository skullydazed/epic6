# randread function

## Synopsis

`$randread(<filename>)`

## Technical

Reads a randomly selected line from a file. The filename is a double-quoted-word argument: double quotes can group a filename containing spaces regardless of `/xdebug dword`. Additional arguments are ignored. Leading `~/` or `~user/` is expanded and the path must resolve to an existing file. Relative paths use the client's working directory; there is no load-path search, decompression, or archive-member lookup.

Selection is based on a random byte offset, not a count of lines. The implementation seeks to one byte before a random position, discards one read, and returns the following read. At end of file it attempts to rewind and read from the beginning. Lines therefore are not selected with equal probability; their lengths and positions affect selection.

## Practical

Use this function for novelty text such as random messages for [/quit](../commands/quit.md), [/kick](../commands/kick.md), [/part](../commands/part.md), or [/topic](../commands/topic.md). Check the returned text before using it as a message.

## Returns

The selected text with trailing newline bytes removed. A carriage return before a newline is retained. A selected blank line returns the empty string, which cannot be distinguished from an early error by the result alone.

Missing filename, path normalization failure, nonpositive file size, or failure to open the file returns the empty string. Later read or rewind failures can instead return diagnostic text beginning `randread: something went really wrong`, with suffix `(1a)`, `(1b)`, `(2)`, `(3)`, or `(4)`. The function closes the opened stream before returning.

## Restrictions

Each read accepts at most `BIG_BUFFER_SIZE - 1` bytes, currently 2047. Long lines can be returned as fragments; discarding a read does not necessarily reach the next line boundary. The function is not encoding-aware and can split multibyte characters. It does not convert file text to UTF-8.

The initial seek result is unchecked. A zero random remainder produces an offset of `-1` on ordinary supported platforms; a failed seek can leave selection starting at the beginning instead. Random-source failure also yields zero through the shared random helper. File changes between size checking and reading can alter the result. This is a local, synchronous file operation and requires no IRC connection.

## Examples

`$randread(~/messages.txt)` returns selected text from an existing `messages.txt` in the user's home directory.

`$randread("/tmp/example messages.txt")` accepts the filename containing a space as one argument.

## Source

Registration and `function_randread()`: [source/functions.c](../../source/functions.c). Filename normalization, file size, randomness, and `chomp()`: [source/ircaux.c](../../source/ircaux.c). Buffer sizes: [include/irc.h](../../include/irc.h).
