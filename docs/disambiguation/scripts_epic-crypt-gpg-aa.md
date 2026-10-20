# epic-crypt-gpg-aa

## Removed helper

`epic-crypt-gpg-aa` was a Bash shell helper for the client's former external message-encryption interface. Both correspondents needed compatible configuration using this helper and the same shared passphrase. It was an external executable, not an EPIC script to load with `/load`.

Unlike [epic-crypt-gpg](scripts_epic-crypt-gpg.md), it converted GnuPG's ASCII-armored output into a single-line representation suitable for the old client's IRC encryption framing. EPIC6 removes external encryption and the former end-to-end encryption command interface, and no longer ships this helper. Neither `encrypt` nor `crypt` is registered in the current command table. Installing the old helper or GnuPG does not restore that integration, and there is no supported EPIC6 invocation or replacement external-cipher setting.

## Historical protocol

The helper accepted `encrypt` or `decrypt` as its first shell argument. It read a first input line containing a passphrase word and discarded additional words. The remaining input supplied the message data. Consequently, the interface did not preserve spaces within a passphrase.

Encryption used `gpg -ca -z 9 --batch --passphrase-fd 0`. The helper removed the armor headers and final footer, then joined the intervening data lines with spaces. This avoided the embedded newlines produced by ordinary GnuPG armor; it did not preserve a complete standalone armored GPG block.

Decryption read that encoded line into a Bash array, generated a temporary armor envelope using GnuPG, inserted the received words as separate lines, and passed the reconstructed block to `gpg -a --batch --passphrase-fd 0`. The passphrase was supplied through standard input. Unknown operation names printed a usage line to standard error.

The implementation required Bash and an external `gpg` in the process search path, and depended on the armor layout emitted by that historical GnuPG interface. Its text representation avoided binary IRC payloads but did not remove IRC line-length limits or provide message splitting. It did not manage public-key recipients or independently authenticate a correspondent's identity. These details describe a removed implementation, not a current GnuPG setup recipe.

Remove obsolete references to this helper from client encryption configuration. TLS server connections protect the connection to the IRC server; they do not recreate this former message-encryption protocol between correspondents.

## History

This helper first appeared in EPIC4-1.1.3. External encryption was retired during EPIC6 development, followed by retirement of the remaining end-to-end encryption command support.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of external encryption, `$crypt()`, `$encryptparm()`, and `/crypt` support.
- [source/commands.c](../../source/commands.c) and [source/functions.c](../../source/functions.c): current command and function registrations omit the former encryption interfaces.
- Repository history: `script/epic-crypt-gpg-aa` in the parent of commit `32f485a5` contains the removed Bash helper, armor conversion, and input protocol; the file is absent from the current tree.
