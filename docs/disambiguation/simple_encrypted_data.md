# Simple Encrypted Data (SED)

SED was a basic obfuscation scheme supplied by older EPIC releases. **Current EPIC6 has removed SED and the former peer/channel conversation-encryption subsystem.** The original statement that the client always offers SED is historical. The current command/function registries and [xform](../functions/xform.md) transform registry do not expose it. See the removed [encrypt](encrypt.md) and [sedcrypt](sedcrypt.md) references.

## Historical algorithm

SED processes bytes without block padding, using a repeating, variable-length key. At the start of each message, set `mix` to zero and begin with the first key byte. For each plaintext byte:

```text
ciphertext_byte = mix XOR plaintext_byte XOR key_byte
mix = mix XOR plaintext_byte
```

Advance to the next key byte, returning to the first key byte after reaching the end. Thus `mix` is the XOR of all earlier plaintext bytes in that message. The historical implementation uses a NUL-terminated password and its byte length; this description assumes a nonempty key.

The original called this an “unbuffered ECB cipher” and inferred ECB from reusing the key. That classification is incorrect: each output byte depends on earlier plaintext through `mix`, whereas ECB independently transforms blocks. The tagged implementation describes SED as a XOR-CBC stream cipher. The recurrence above states precisely what the code does without treating that historical name as a standard cryptographic construction.

A longer key delays repetition within a message. Key bytes beyond the plaintext's length are not used for that message. Neither observation supports the original's implication that increasing key length makes SED secure; the key and zero initial state are reused across messages.

## Worked example

For plaintext `testing` and key `hello`, the first two bytes are:

| Byte | Initial mix | Plaintext byte | Key byte | Output byte | Updated mix |
| --- | --- | --- | --- | --- | --- |
| 1 | `0x00` | `0x74` (`t`) | `0x68` (`h`) | `0x1c` | `0x74` |
| 2 | `0x74` | `0x65` (`e`) | `0x65` (`e`) | `0x74` | `0x11` |

For example, byte two produces `0x74 XOR 0x65 XOR 0x65 = 0x74`, then updates the mix to `0x74 XOR 0x65 = 0x11`. Continue the same procedure for the remaining bytes. This corrects the original first key byte's `0x0x68` typo.

## Weaknesses and historical motivation

SED is not a serious encryption scheme. Treat it as obfuscation, not protection against someone who wants to recover or modify the message. Its repeated-key XOR construction does not provide authenticated encryption.

The original rationale was that non-plaintext messages might frustrate simple server-side pattern matching, making automated scanning less convenient. Its assumptions about how surveillance was performed and whether an operator would bother decrypting are not guarantees supplied by the algorithm.

Older EPIC's encryption handler recursively processed a decrypted message as CTCP. With compatible peers and configured conversation encryption, a CTCP DCC SEND offer could therefore be carried inside an encrypted message. Other clients did not necessarily support this nesting. The original presented this as a way to obscure file-transfer offers from server-side logging, and alleged that some networks logged offers and sent them to government agencies; the implementation establishes the nested-CTCP mechanism, not those claims about operators or surveillance.

Both the legacy conversation-encryption facility and [DCC SEND](ctcp_send.md) are removed from current EPIC6, so that historical workflow is unavailable. Current [TLS server connections](../special/about_ssl_connections.md) are a separate transport facility and do not restore SED or peer-to-peer message encryption.

## Source validation

- Tag `epic5-2.1.6`, `source/crypto.c`: `encrypt_sed`, `decrypt_sed`, the byte recurrence, password handling, and historical security comments.
- The same tag's `source/ctcp.c`: SED handler registration and recursive CTCP processing after decryption; its `source/ircaux.c` registered the former SED transform.
- Current [KNOWNBUGS](../../KNOWNBUGS): retirement records for SED, conversation encryption, and DCC.
- Current [source/commands.c](../../source/commands.c), [source/functions.c](../../source/functions.c), [source/ctcp.c](../../source/ctcp.c), and [source/ircaux.c](../../source/ircaux.c): current command/function, CTCP, and transform registries without those SED facilities.
