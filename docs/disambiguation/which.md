# Which

Do you mean:

- The [which command](../commands/which.md), `/which file [file ...]`, which uses the load-file machinery and normally displays resolved filenames without executing their script text?
- The [which function](../functions/which.md), `$which(file [directory-search-path])`, which returns the first existing canonical path or an empty string on failure?

The command opens and reads files, with the shared loader's encoding and compression behavior; some loader options can change its default mode. The function only searches for an existing path and does not open, decompress, or execute the result. Their argument parsing and compressed-file handling differ.

Both use [load_path](../set/load_path.md) by default. The function can instead take an explicit colon-separated search path; it does not search the shell's executable `PATH` automatically or prove that a match is readable or executable. See the individual pages for path expansion, options, errors, and filenames containing spaces.

## Source references

- `source/commands.c`: shared load handler and path-display loader.
- `source/functions.c`: `which()` filename and search-path arguments.
- `source/ircaux.c`: filesystem path search and normalization helpers.
