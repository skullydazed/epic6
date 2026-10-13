# xform

Transform text through a chain of encodings, hashing, or character-set conversion.

## Synopsis

```irc
$xform(<transformations> [meta ...] <text>)
```

Quote a space-separated transformation chain as one argument. Both that argument and each required meta argument always honor double-quoted words, independently of `/xdebug dword`. The complete remaining text is the input data.

## About transformations

Encoding represents data in a form usable as script text and can generally be reversed for valid inputs. Hashing produces a digest from which the original data cannot be recovered. Character-set conversion changes the representation of text and can lose characters. The current registry does not provide the older encryption transforms.

## Supported transformations

Names are case-insensitive and must match exactly. Prefix `+` selects encoding; an unsigned name also selects encoding. Prefix `-` selects decoding.

| Name | Behavior | Meta argument |
| --- | --- | --- |
| `url` | Percent encoding using uppercase hex and RFC3986 unreserved characters; decoding does not convert `+` to space | None |
| `enc` | Two A–P characters per byte, representing its high and low nibbles; this is not ordinary 0–9/A–F hexadecimal | None |
| `b64` | Base64 encoding/decoding | None |
| `b85` | ASCII85 encoding/decoding with `<~...~>` wrappers | None |
| `ctcp` | EPIC CTCP quoting, including representation of NUL, newline, and carriage return | None |
| `none` | Copy the data unchanged | None |
| `len` | Replace data with its decimal byte length | None |
| `sha256` | SHA-256 digest as hexadecimal text | None |
| `iconv` | Convert between system-supported character encodings | `FROM/TO`, or a signed converter handle |
| `all` | Replace data with the space-separated uppercase registry names | None |

`len`, `sha256`, `iconv`, `all`, and `none` use the same routine for both directions: `-sha256` does not recover hashed data, and `-iconv` does not automatically reverse the encoding pair. No cipher key is accepted by any currently registered transform. Names such as `sed`, `bf`, `cast`, `aes`, `aessha`, `fish`, `fish64`, and `def` are not registered.

## Description

Transformations run left to right. Supply one meta argument for each transform that requires one, in chain order, before the text. At most 16 recognized transformations are collected; later directives are not processed. Unknown names are silently skipped and do not consume a meta argument. If every name is unknown, the remaining text is copied unchanged.

Intermediate data lengths are retained, permitting a decoder to produce binary data containing NUL bytes and pass it to another transform in the same call. End a binary chain with an encoding such as `ctcp`, `url`, or `b64` before returning it to script text. A final unencoded NUL truncates the visible C-string result.

## iconv transformation

For example, `iconv UTF-8/ISO-8859-1` converts from UTF-8 to ISO-8859-1 regardless of the sign on the transformation name. Alternatively, use `+<handle>` or `-<handle>` as its meta argument to select the forward or reverse converter created by [iconvctl](iconvctl.md). The sign on the **meta argument**, rather than on `iconv`, selects that direction. Use an existing nonnegative handle after the sign.

Available encoding names depend on the system's iconv implementation. Failed converter opening produces no converted bytes. Invalid or incomplete input bytes are skipped; other conversion failures can leave partial output. Conversion is not guaranteed to be lossless and does not automatically flush a stateful encoding's final shift sequence.

## Returns and restrictions

The final transformed string. Missing transformation or required meta arguments return empty. Individual codecs have different malformed-input behavior; there is no uniform error result or validation. In particular, negative codec results are converted to unsigned lengths without checking before subsequent processing and final termination. A failing decoder can therefore cause memory errors rather than a clean empty result.

Both working buffers have a fixed capacity of ten times the original text's byte length plus 256 bytes, reused throughout the chain. Repeated expanding transforms can exceed it; results can be truncated, and codecs that do not enforce capacity can overrun it. Keep chains and inputs within that capacity rather than assuming arbitrary composition is safe. Hashing and codec output do not depend on floating-point display settings. The function is always registered and uses the build's required crypto library and iconv support.

## Examples

```irc
$xform(+url this is a string)       /* this%20is%20a%20string */
$xform(-url this%20is%20a%20string) /* this is a string */
$xform(enc A)                      /* EB */
$xform("-enc +ctcp" EB)           /* A */
$xform(len café)                   /* 5 for UTF-8 text. */
$xform(all)                        /* List the current transform names. */
$xform(iconv UTF-8/ISO-8859-1 $utf8string)
```

For binary file output, prepare CTCP-quoted data and pass it to [writeb](writeb.md). Do not use [read](read.md) as an arbitrary binary reader: its ordinary line form returns line text, while its explicit byte-count form returns CTCP-encoded bytes.

## Source

Registered and implemented as `function_xform` in [source/functions.c](../../source/functions.c). The registry, codecs, `lookup_transform`, `transform_string`, and iconv helpers are in [source/ircaux.c](../../source/ircaux.c).
