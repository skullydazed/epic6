# Sedcrypt: removed encryption diagnostic

The former `$sedcrypt(flag peer text)` debugging function applied the configured peer's conversation-encryption settings. Flag `0` modeled incoming text and flag `1` modeled outgoing text. Ciphertext used CTCP quoting; that quoting was not a guarantee that it could be displayed directly as ordinary text.

EPIC6 has removed the legacy SED/conversation-encryption facilities and no longer registers `sedcrypt()`. No bundled script replaces it. There is no current peer-key lookup, incoming/outgoing transformation, supported flag set, or result/error contract under this name.

See [encrypt](encrypt.md) for the removed conversation command and [encryptparm](encryptparm.md) for its removed metadata query. The current [xform](../functions/xform.md) registry does not include a SED cipher. Its CTCP quoting transform only represents bytes safely as text; it does not restore encryption or select peer keys.

An old diagnostic call cannot establish that a message is encrypted, decrypt retained SED ciphertext, or re-enable the former subsystem. Current IRC transport TLS is a separate feature from peer/channel encryption.

## History and source

`sedcrypt()` first appeared in EPIC4-1.1.14.

- `source/functions.c`: current registry omits `sedcrypt`.
- `source/ircaux.c`: current transform registry without SED encryption.
- `UPDATES` and `KNOWNBUGS`: retirement of legacy cryptography and conversation encryption.
