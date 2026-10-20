# epic-crypt-gpg

## Removed helper

`epic-crypt-gpg` was a Bash shell helper for the client's former external message-encryption interface. Both correspondents needed compatible external-cipher configuration using the same shared passphrase. It was an external executable, not a script to load with `/load`.

EPIC6 removes external encryption and the former end-to-end encryption command interface. The helper is no longer shipped, and the current command table registers neither `encrypt` nor `crypt`. Installing a copy of the old helper or GnuPG does not restore that client integration. There is no supported EPIC6 invocation or replacement external-cipher setting for this facility.

## Historical protocol

The helper accepted `encrypt` or `decrypt` as its first shell argument. It read the first standard-input line as a passphrase word followed by discarded words, then treated the remaining input as binary message data. The first whitespace-separated word was the key, so a passphrase containing spaces was not preserved by that interface.

For encryption it invoked `gpg -c -z 9 --batch --passphrase-fd 0`; for decryption it invoked `gpg --batch --passphrase-fd 0`. It supplied the passphrase through standard input before the message data. An unrecognized operation printed a usage line to standard error. The script depended on Bash and an external `gpg` executable in the process search path.

Encryption output was binary rather than ASCII-armored. It was intended for the old client's external-cipher protocol, not for pasting raw output into an IRC message. This helper did not by itself supply IRC-safe text framing, public-key recipient management, or an independently authenticated correspondent identity. These historical details describe the removed implementation, not a current GnuPG setup recipe.

Remove obsolete references to this helper from client encryption configuration. TLS server connections are a separate facility: they encrypt the connection to the server and do not recreate this message-encryption protocol between correspondents.

## History

This helper first appeared in EPIC4-1.1.3. Its former external-program interface was retired during EPIC6 development, followed by retirement of the remaining end-to-end encryption command support.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of external encryption, `$crypt()`, `$encryptparm()`, and end-to-end encryption through `/crypt`.
- [source/commands.c](../../source/commands.c): current command registrations omit the former encryption commands.
- [source/functions.c](../../source/functions.c): current function registrations omit the former encryption-control functions.
- Repository history: `script/epic-crypt-gpg` in the parent of commit `32f485a5` contains the removed Bash helper and its input protocol; the file is absent from the current tree.
