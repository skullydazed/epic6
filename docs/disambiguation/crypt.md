# Crypt: removed password-hash function

The former `$crypt(string salt)` function delegated Unix-style password hashing to the host's `crypt(3)` library. Its arguments used double-quoted-word parsing, and its result depended on the host library and salt format. It did not decrypt data; “one-way encryption” in descriptions of that interface meant password hashing.

EPIC6 no longer registers `crypt()` and supplies no bundled script replacement. There is no current built-in salt format, eight-character input rule, return value, or platform-library option for that function. Old calls such as `$crypt(foobar ab)` do not provide a supported password-hashing operation in this client.

This is distinct from the removed [encrypt](encrypt.md) conversation-encryption command. It is also distinct from [sha256](sha256.md) and [xform](../functions/xform.md): a plain digest is not a compatible Unix password-hash record, and the current transform registry does not restore `crypt(3)` or the former cipher interfaces.

If an application requires a particular password-hash format, its implementation must explicitly support that format and verification rules; neither the old name nor an arbitrary replacement digest establishes compatibility. See [exec](../commands/exec.md) for external-program integration where an application supplies its own tool.

## Source references

- `source/functions.c`: current registry has no `crypt` function.
- `source/ircaux.c`: current transformation registry.
- `KNOWNBUGS`: explicit sunset of `crypt()`.
- `UPDATES`: retirement of legacy cryptographic facilities.
