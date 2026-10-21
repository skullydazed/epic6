# Encryptparm: removed conversation metadata

The former `$encryptparm(nickname)` function queried a SED-encrypted conversation on the current server. It returned quoted-word metadata containing the nickname, cipher key, and external encryption program. A possible fourth cipher-type field was a historical proposal, not a current interface.

EPIC6 has retired `encryptparm()` together with the legacy encryption facilities. The function is absent from the current registry, and no bundled script replaces it. There is no supported metadata tuple, key lookup, missing-conversation result, or cipher-type extension to query under this name.

See [encrypt](encrypt.md) for removal of the conversation-encryption command and [crypt](crypt.md) for the separate retired password-hash function. The current [xform](../functions/xform.md) encodings and digests do not create a conversation key table or restore this metadata query. TLS server-connection state is also separate from the former peer/channel encryption records.

For parsing quoted words in other current interfaces, see [qword](../functions/qword.md) and [what is a word?](../special/what_is_a_word.md); their continued existence does not imply an encryptparm result is available.

## History and source

The function appeared in EPIC4-1.1.3.

- `source/functions.c`: current function registry omits `encryptparm`.
- `KNOWNBUGS`: explicit sunset of `crypt()` and `encryptparm()`.
- `UPDATES`: retirement of legacy cryptographic facilities.
