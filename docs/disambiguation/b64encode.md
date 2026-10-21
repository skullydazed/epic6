# B64encode

`$b64encode(text)` is a compatibility function supplied by [builtins](../scripts/builtins.md), not a registered C built-in. It returns `$xform(+b64 text)`, converting the input bytes to ordinary Base64. The reverse wrapper is [b64decode](b64decode.md).

The direct [xform](../functions/xform.md) equivalent is:

```irc
$xform(+b64 hello)
```

This returns `aGVsbG8=`. Base64 converts each three-byte group to four characters from `A`–`Z`, `a`–`z`, `0`–`9`, `+`, and `/`, using `=` padding for a final partial group. This encoder does not add MIME line wrapping. Empty input yields an empty result.

The wrapper is available only after its defining script is loaded; default startup normally loads `builtins`. Its input is an EPIC C string, so embedded NUL bytes cannot be passed intact as ordinary function text. For binary transformations, keep the binary-producing operation and final Base64 encoding in one xform chain, which retains intermediate lengths.

Base64 is an encoding, not encryption or authentication. It produces text characters but does not override IRC message-length limits or other protocol restrictions. It is also distinct from the former FiSH encoding; the current xform registry does not provide `fish64`.

## Source references

- `script/builtins`: encoding and decoding wrappers.
- `source/ircaux.c`: Base64 alphabet, padding, encoder, and transform registry.
- `source/functions.c`: xform interface and current built-in registry.
