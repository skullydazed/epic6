# Urlencode

`$urlencode(text)` is a compatibility alias in [builtins](../scripts/builtins.md), not a registered C built-in. It returns `$xform(+url text)`. Its defining script must be loaded; default startup normally loads it. [urldecode](urldecode.md) supplies the reverse wrapper.

The direct [xform](../functions/xform.md) equivalent is:

```irc
$xform(+url hello there)
```

This returns `hello%20there`. The encoder preserves ASCII letters, digits, `-`, `_`, `.`, and `~`. Every other byte becomes `%` followed by two uppercase hexadecimal digits. A space becomes `%20`, not `+`; a literal plus becomes `%2B`. Empty input returns empty.

The operation encodes bytes without character-set conversion. UTF-8 characters can therefore produce several percent escapes. It does not parse a URL or preserve reserved separators such as `/`, `:`, `?`, and `&`, so apply it to the component or data value that needs escaping rather than blindly treating an entire URL as one component.

Ordinary function input is a C string, so embedded NUL bytes cannot be supplied intact that way. A single xform chain can retain binary intermediate lengths and end with URL encoding to return text safely. See xform for chain syntax and limits.

Percent encoding is not a general substitute for shell quoting, path validation, or protocol validation. [exec](../commands/exec.md) and any later decoding/evaluation stage have their own argument rules. This function performs no network request or file transfer.

## Source references

- `script/builtins`: URL compatibility wrappers.
- `source/ircaux.c`: unreserved-byte table, percent encoder, and transform registration.
- `source/functions.c`: xform interface and current registry.
