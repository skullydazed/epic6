# B64decode

`$b64decode(text)` is now a script-provided compatibility function in [builtins](../scripts/builtins.md), not a registered C built-in. It returns `$xform(-b64 text)`, decoding ordinary Base64 text into bytes. The reverse wrapper is [b64encode](b64encode.md).

For direct use, see [xform](../functions/xform.md):

```irc
$xform(-b64 aGVsbG8=)
```

This returns `hello`. The wrapper is available only when its defining script has been loaded; default startup normally loads `builtins`.

Base64 encodes groups of three bytes as four characters, with padding for a final partial group. A final decoded NUL byte inside the returned data terminates the visible C-string result, so the wrapper is unsuitable for returning arbitrary binary data intact. For binary work, keep decoding and a final text-safe encoding in one xform chain; intermediate lengths are retained there.

The decoder stops on a decoding error and can return an already decoded prefix rather than a separate validity flag. Do not treat a nonempty result as proof that all input was valid. This is ordinary Base64, not the former FiSH encoding; the current transform registry does not provide `fish64`.

## Source references

- `script/builtins`: `b64decode` and `b64encode` wrappers.
- `source/ircaux.c`: `b64` transform registry and decoder.
- `source/functions.c`: `xform` interface and current built-in registry.
