# info function

## Synopsis

`$info(<selector>)`

## Technical

Returns information stored in the client about its version and compilation. This is a local query, distinct from the [info command](../commands/info.md), and sends nothing to an IRC server.

The first function argument word selects a field; extra words are ignored. Selectors are case-insensitive. Ordinary selectors match only their first letter, so `version` also selects `v`. Missing input or an unrecognized selector returns empty without a diagnostic.

## Options and returns

| Selector | Returned information |
| --- | --- |
| `c` | Compilation description containing the build user, host, and time. |
| `i` | Numeric source `commit_id`; a manually maintained identifier, not a Git hash. |
| `m` | `1`: the new math parser is used. |
| `o` | Compact compile-time option letters, described below. |
| `r` | Release's “silly name.” |
| `s` | Build-time `cksum` output for `source/info.c.sh`, including checksum, size, and filename. This is not a checksum of the client executable. |
| `v` | Space-separated version identification, currently shaped like `epic6 0 0 1`. |
| `w` | `1`: local-variable support. |
| `z:configure_args` | Recorded configure arguments. |
| `z:compiler_version` | Compiler version output recorded during the build. |
| `z:final_link` | Recorded final executable link recipe. |
| `z:cflags` | Recorded compiler flags definition. |
| `z:libs` | Recorded library flags definition. |
| `z` | A JSON-shaped object containing the five preceding build fields. |

The `o` string currently contains `h` for implied hooks, `p` when built with PCRE2, and `r` when built with libarchive, in that order. It does not describe every client feature; old option letters should not be used to test current features.

The extended `z` selectors are checked by fixed prefixes: the first eight characters for configure/compiler fields and six for link/cflags/libs. Use the full spellings above. Other words beginning with `z` return the aggregate object. That object interpolates field text without JSON escaping, so quotes, backslashes, or newlines in build metadata can make it invalid JSON; individual field queries are safer for arbitrary build data.

## Practical

Use this function to report the client's version or diagnose how it was built. The fields describe the compiled client, not current runtime settings or the current state of its source checkout. Their exact text varies by build.

## Examples

```irc
@ build_description = info(c)
@ source_identifier = info(i)
@ compiler_flags = info(z:cflags)
```

`$info(m)` and `$info(w)` return `1`. `$info(o)` may return `h`, `hp`, `hr`, or `hpr` according to the enabled optional libraries.

## Source

Registration and selector handling: [source/functions.c](../../source/functions.c). Option letters: [include/options.h](../../include/options.h). Version, source identifier, and release name: [source/irc.c](../../source/irc.c). Generated compilation metadata and script checksum: [source/info.c.sh](../../source/info.c.sh).
