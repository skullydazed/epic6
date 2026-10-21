# Urldecode

`$urldecode(text)` is a compatibility alias in [builtins](../scripts/builtins.md), not a registered C built-in. It returns `$xform(-url text)`. Its defining script must be loaded; default startup normally loads it. The reverse wrapper is [urlencode](urlencode.md).

The direct [xform](../functions/xform.md) form is:

```irc
$xform(-url hello%20there)
```

This returns `hello there`. A percent sign followed by two hexadecimal digits is decoded to the corresponding byte; other bytes are copied. Hexadecimal letters are accepted in either case. Malformed percent sequences remain literal rather than producing a separate validation error. A plus sign stays `+`: this is not HTML form decoding that converts plus signs to spaces. Empty input yields empty.

The decoder does one pass and does not parse a URL into components, fetch a resource, or perform character-set conversion. `%2520` therefore becomes `%20`, not a space. The returned text can contain punctuation meaningful to later script or shell evaluation; decoding does not provide quoting for those later contexts.

Ordinary script values are C strings. `%00` produces a NUL, truncating the visible final result. To process binary data safely, keep decoding and a final text-safe encoding in one xform chain, which retains intermediate lengths. See xform for supported transforms and result limitations.

## Source references

- `script/builtins`: URL compatibility wrappers.
- `source/ircaux.c`: percent decoder and URL transform registration.
- `source/functions.c`: xform entry point and current function registry.
