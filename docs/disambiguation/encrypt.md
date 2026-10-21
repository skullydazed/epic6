# Encrypt: removed conversation encryption

The former `/encrypt` command configured encrypted conversations with nicknames, channels, or direct-chat targets. It accepted target/key/cipher information, listed configured conversations without arguments, and used `-clear` to remove them all. That built-in conversation-encryption subsystem is removed in EPIC6.

There is no current `/encrypt` handler, conversation key table exposed by that command, or bundled replacement. Historical SED, CAST, Blowfish, AES/AESSHA/SEDSHA, server-qualified target, and external-program examples do not configure encryption in the current client. The former overlap-selection rules and password/cipher syntax are not a current supported interface.

## Related features are separate

TLS for an IRC server connection protects the client-to-server transport; it does not restore the former peer/channel encryption command or provide end-to-end encryption between chat participants. See [server descriptions](../special/server_description.md) for transport selection.

[xform](../functions/xform.md) still provides encodings, hashes, and character-set conversion, but its current registry contains no encryption transforms such as AES, SED, CAST, or FiSH. Base64 and SHA-256 are not replacements for conversation encryption. An external program launched through [exec](../commands/exec.md) also does not automatically encrypt outgoing IRC messages or decrypt incoming ones.

Repository notes discuss a possible future return of end-to-end encryption. That is development direction rather than an available interface, cipher guarantee, or timetable. Use documentation for a concrete current implementation before assuming messages are encrypted.

## Source references

- `source/commands.c`: current command registry omits `encrypt` and the former conversation-encryption handler.
- `source/ircaux.c`: current xform registry contains no cipher transformations.
- `source/server.c` and `source/ssl.c`: separate IRC transport TLS handling.
- `UPDATES` and `KNOWNBUGS`: retirement and possible redesign of end-to-end encryption.
