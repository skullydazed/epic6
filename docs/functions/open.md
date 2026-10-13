# $open()

## Synopsis

`$open(<filename> <mode>)`

## Technical

Opens a file and returns an EPIC file handle for [$read()](read.md), [$write()](write.md), and eventually [$close()](close.md). This handle is a client-managed integer, not an operating-system file descriptor. Keep it until the file is closed; [$isfilevalid()](isfilevalid.md) checks whether it remains registered.

The filename is always a double-quoted word, so paths containing spaces can be quoted independently of `/xdebug dword`. The mode uses normal function word parsing. Only one filename and mode are used; later arguments are ignored.

## Modes

| Mode | Behavior for ordinary files |
| --- | --- |
| `R` or `r` | Read from the beginning using EPIC's file-search/decompression helper |
| `W` or `w` | Append to the file, creating it if needed |
| `rb` | Read directly through the ordinary file-opening path |
| `a` or `ab` | Append, creating the file if needed |
| `wb` | Create or truncate the file and open for writing |
| `r+` | Read and write an existing file without truncation |
| `w+` | Create or truncate and open for reading and writing |
| `a+` | Read and append, creating the file if needed |

The single-letter R/W aliases are case-insensitive. Other mode strings are passed unchanged to the system `fopen()` through EPIC's file wrapper; their validity and extensions depend on the platform. In particular, bare `w` means **append** here, while `wb` or `w+` truncates an ordinary existing file. See the [POSIX fopen specification](https://pubs.opengroup.org/onlinepubs/9699919799/functions/fopen.html) for stream modes and read/write switching rules.

## Paths and read mode

R mode searches from the current directory, not the script load path, and supports tilde expansion. If the requested name is not found, it also tries compressed suffixes `.gz`, `.Z`, `.z`, and `.bz2`. Decompression uses external programs found in `PATH`: `gunzip`, `uncompress`, or `bunzip2`/`bunzip`, as appropriate. The helper rejects directories and files whose recorded mode has any execute bit set.

Explicit compressed filenames take a path through the helper that does not initialize the file-stat information before the execute-bit check. Their acceptance is therefore not dependable in the current implementation. Compressed streams are not ordinary seekable files.

For W and other modes, EPIC first tries filename normalization. If that fails, it passes the **original** filename to the file wrapper. Existing paths can have tilde expansion and canonicalization, but a new `~/file` is not reliably expanded because normalization requires existence. Use an already expanded path when creating a file.

When built with libarchive, names containing `.zip` or `.tar` are examined as archive paths. `archive.zip/member` or `archive.tar/member` selects a member; opening the archive without a member selects `.ircrc`. Archive handling happens before the ordinary `fopen()` call and does not honor its requested mode: a successful archive selection produces a reading stream even if W or a write mode was supplied. Archive support depends on the build and libarchive's supported formats/filters; it does not create or update archive entries.

## Practical

Use R for line-oriented file reading and W for append-only output. For a new ordinary output file that should replace its previous contents, a passed-through truncating mode such as `wb` is available. Close successful handles when finished. Opening does not automatically read, write, or execute the file's contents, although creation or truncation can occur immediately according to the mode.

Archive and decompression handles have limitations in positioning and writing operations; do not assume every operation supported by an ordinary file is valid for them.

## Returns

A positive EPIC file handle on success, or `-1` when opening fails. Missing filename or mode, or an empty mode, returns the empty string. Failures can print file-search, permission, executable-file, decompressor, or archive diagnostics. A returned handle does not prove it has the requested write capability when archive handling selected a read stream.

## Examples

```text
$open(~/.irclog R)               read an existing logfile
$open(session.log W)             append to an ordinary local logfile, creating it if needed
$open("notes with spaces.txt" R) read one path containing spaces
$open(output.txt wb)             create or truncate an ordinary output file
```

Actual return values depend on filesystem state, permissions, mode, and optional archive/decompression support.

Registration and `function_open()`: [source/functions.c](../../source/functions.c). File-handle allocation and read/write opening: [source/files.c](../../source/files.c). Search, tilde expansion, normalization, and decompression: [source/ircaux.c](../../source/ircaux.c). Ordinary and archive streams: [source/elf.c](../../source/elf.c).
