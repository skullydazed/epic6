# Encode

`$encode(text)` is a compatibility function supplied by [builtins](../scripts/builtins.md), not a registered C built-in. It returns `$xform(+enc text)`. The defining script must be loaded; default startup normally loads it. [decode](decode.md) supplies the inverse wrapper.

The current `enc` transformation converts each input byte into two uppercase letters `A` through `P`, representing its high and low four-bit nibbles. It is deterministic, preserves case distinctions in the input, and produces twice the input byte length. Empty input returns empty. This is not ordinary hexadecimal, Base64, encryption, or a hash.

```irc
$encode(hello there)
$decode($encode(hello there))
```

The first returns `GIGFGMGMGPCAHEGIGFHCGF`; the second returns `hello there`. Prefer generating encoded keys at runtime rather than unnecessarily coupling scripts to hardcoded representations.

## Structure keys

The output alphabet is suitable for variable-name components. For example, in an EPIC script:

```irc
@ key = encode([some nickname])
@ users[$key] = [cached value]
```

This avoids inserting spaces or punctuation from arbitrary text into the variable name. Different input byte strings produce distinct encoded keys; if an application wants case-insensitive identity, it must normalize the input consistently before encoding. Empty input produces no component, so handle empty keys explicitly rather than assuming a nonempty identifier.

Ordinary script arguments are C strings and cannot carry embedded NUL bytes intact. For transformations that produce binary data, use one [xform](../functions/xform.md) chain ending in a text-safe encoding; intermediate lengths are retained there. Encoding does not validate the meaning or freshness of cached user, channel, or server data.

## Source references

- `script/builtins`: `encode` and `decode` wrappers.
- `source/ircaux.c`: nibble encoder, decoder, and transform registry.
- `source/functions.c`: xform interface and current built-in registry.
- `source/alias.c`: bracketed variable-name expansion and structure handling.
