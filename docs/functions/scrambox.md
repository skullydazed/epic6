# $scrambox()

## Synopsis

`$scrambox(reset)`

`$scrambox(begin <username> <password>)`

`$scrambox(response <server message>)`

## Technical

Maintains client-side SCRAM-SHA-512 authentication state for the current execution server. It generates authentication messages and verifies a server signature; it does not send IRC commands or establish authentication by itself. The bundled [SASL script](../../script/sasl) handles message transport and server success/failure replies.

Operation names are case-insensitive. `begin` takes two double-quoted words, so double quotes group credentials containing spaces regardless of `/xdebug dword`; additional arguments are ignored. `response` takes the entire remaining text as an already-decoded SCRAM message. Do not pass the outer SASL Base64 encoding or quote the message merely to group it.

| Operation | Effect and result |
| --- | --- |
| `reset` | Clears and frees this server's stored state; returns empty |
| `begin` | Stores credentials and creates a fresh random nonce; returns the client-first-message-bare, `n=<username>,r=<nonce>` |
| First `response` | Processes the server-first message containing nonce, salt, and iteration count; returns a client-final message with `c=biws`, nonce, and proof |
| Later `response` | Checks the server-final message's `v=` signature; returns `+` on successful verification |

State is keyed by the current server refnum, with supported refnums `0` through `127`. There is no explicit server selector. The response phase is chosen by whether a client-final-message-bare has already been stored. Call `reset` before each new exchange: `begin` does not clear all previous state, and failed operations do not automatically reset it.

## Practical

Use the bundled SASL script for an IRC authentication exchange. A caller using this primitive directly must retain the same server context, add the GS2 header `n,,` to the first returned message, and perform the outer SASL encoding and transport separately. Embedded salt, nonce, proof, and signature encodings are handled by the helper itself.

## Returns

The generated message, `+` after successful server-signature verification, or the empty string on failure. Missing input or an unknown operation also returns empty. Missing credentials return empty. Unsupported server refnums and authentication-processing failures print diagnostics; `reset` silently does nothing for an unsupported refnum. Empty alone therefore does not distinguish reset, invalid syntax, and authentication failure.

A successful signature check is not an IRC server's confirmation that authentication has completed; the script separately handles the server's success numeric.

## Restrictions

This built-in is always registered and uses the build's OpenSSL and libsodium dependencies. It implements SCRAM-SHA-512 without channel binding (`c=biws`). Its purported SASLPrep helper currently only copies strings: it does not normalize Unicode or escape special username characters. Username and password storage is limited to 255 bytes, with longer values silently truncated. Other exchange fields also have fixed-size buffers and can fail or truncate when oversized.

[/xdebug scram](../commands/xdebug.md) can log credentials and exchange details through this implementation's debugging calls.

## Examples

`$scrambox(reset)` clears the current server's exchange state and returns empty.

After reset, `$scrambox(begin example example-password)` returns a message beginning `n=example,r=` followed by a randomly generated nonce. Its exact value varies. It does not send that message to a server.

Registration: [source/functions.c](../../source/functions.c). Operations, state storage, message generation, and verification: [source/scrambox.c](../../source/scrambox.c). Build dependencies: [configure.ac](../../configure.ac). Protocol orchestration: [script/sasl](../../script/sasl).
