# iconvctl function

## Synopsis

`$iconvctl(add <fromcode>/<tocode>[/<option>])`

`$iconvctl(list)`

`$iconvctl(size)`

`$iconvctl(get <id>)`

`$iconvctl(remove <id>)`

## Technical

Manages reusable character-encoding converters for [$xform()](xform.md). `add` opens both a forward converter from `fromcode` to `tocode` and a reverse converter in the opposite direction. Encoding names and any option suffix are interpreted by the system iconv implementation; both directions must open successfully.

Operations are case-insensitive and accept prefixes. Encoding specifications are one function argument word; quote-aware parsing follows `/xdebug dword`. IDs are zero-based integers, allocated in the first free slot and reused after removal. Additional arguments are ignored. Missing required arguments or unknown operations return empty.

## Operations and returns

| Operation | Result |
| --- | --- |
| `add <specification>` | New converter ID, including valid ID `0`; empty if the specification lacks `/` or either conversion cannot be opened. |
| `list` | Active IDs in ascending order, or empty. |
| `size` | Allocated slot count, including holes; empty before any converter has been allocated. |
| `get <id>` | Original stored specification; `0` for an empty slot; empty for an out-of-range ID. |
| `remove <id>` | Closes both converters and frees the slot; `1` on success, `0` for an already empty interior slot, empty for an out-of-range ID. |

After allocation, removing converters may shrink trailing empty slots, but an empty slot zero can remain allocated. Consequently `size` is not the number of active converters; use `list` for active IDs. If no slots exist, all operations except `add` return empty.

IDs use base-detecting integer conversion for `get` and `remove`; malformed numeric text can select ID zero. Check for an empty `add` result rather than testing its numeric truth value.

## Using a converter

Pass `+<id>` as the iconv transform's metadata for forward conversion and `-<id>` for reverse conversion:

```irc
@ converter = iconvctl(add UTF-8/ISO-8859-1)
if (strlen($converter)) {
    @ forward_text = xform(iconv +$converter hello)
    @ reverse_text = xform(iconv -$converter $forward_text)
    @ iconvctl(remove $converter)
}
```

Both results are `hello` if the platform supports these encodings. The metadata sign selects direction; the `+iconv` or `-iconv` transform directive itself does not reverse this converter, because both directive forms use the same conversion routine.

Conversion preserves each descriptor's state across calls. There is no reset operation or final shift-state flush; remove and recreate the converter to obtain fresh state. Forward and reverse state are separate.

## Restrictions

Converter IDs are local to the running client and can be reused; do not retain an ID after removing it. Supply a valid nonnegative ID in transform metadata: that lookup does not safely reject negative numeric IDs.

The xform iconv routine skips bytes that cause invalid or incomplete input errors and can return partial output on other errors or output-buffer exhaustion. It does not provide a separate success/error result. Empty output is not proof of successful conversion. Converter-open diagnostics are normally available only through Unicode debugging.

Conversions can produce NUL bytes, which ordinary script strings cannot safely retain. To preserve binary output, chain `+ctcp` after iconv in the same xform call; decode it with `-ctcp` before the reverse conversion. For example, `$xform("iconv +ctcp" +<id> <text>)` returns CTCP-encoded output, and `$xform("-ctcp iconv" -<id> <encoded-text>)` reverses that chain. This encoding is explicit; `iconvctl` does not apply it automatically.

The built-in is registered unconditionally. Iconv support is required by the current configure script, with `--with-iconv=<path>` available for locating a separate installation. Available encoding names and suffixes depend on that installation. This manages xform converters, independently of the automatic message rules controlled by [encoding](../commands/encoding.md) and [$encodingctl()](encodingctl.md).

## Source

Registration and all control operations: [source/functions.c](../../source/functions.c). Converter opening, descriptor storage, xform conversion, and CTCP transforms: [source/ircaux.c](../../source/ircaux.c). Build requirements: [configure.ac](../../configure.ac).
