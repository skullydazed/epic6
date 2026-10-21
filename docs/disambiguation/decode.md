# Decode

`$decode(text)` is a compatibility alias supplied by [builtins](../scripts/builtins.md). It is no longer a registered C built-in; its implementation returns `$xform(-enc text)`. Load the defining script before using the wrapper. Default startup normally loads it.

The [xform](../functions/xform.md) `enc` transformation represents each byte as two uppercase letters `A` through `P`, encoding its high and low nibbles. It is not ordinary hexadecimal or Base64. [encode](encode.md) supplies the matching encoding wrapper.

For example:

```irc
$decode($encode(hello there))
$xform(-enc GIGFGMGMGPCAHEGIGFHCGF)
```

Both return `hello there`. Empty input returns empty. Use values produced by the matching encoding; malformed input is not a reliable validation interface. Prefer generating encoded values at runtime instead of depending unnecessarily on hardcoded compatibility strings.

Returned script text is a C string. A decoded NUL terminates its visible value, so this wrapper does not return arbitrary binary data intact. For binary processing, keep decoding and a final text-safe transformation in one xform chain, which preserves intermediate lengths. See xform for details.

This is reversible representation, not encryption, and sends no IRC request.

## Source references

- `script/builtins`: `decode`/`encode` wrappers.
- `source/ircaux.c`: `enc` encoder, decoder, and transform registration.
- `source/functions.c`: xform interface and current function registry.
