# $ecdsatool()

## Synopsis

```text
$ecdsatool(keygen <private-key-file>)
$ecdsatool(pubkey <private-key-file>)
$ecdsatool(sign <private-key-file> <base64-challenge>)
$ecdsatool(verify <private-key-file> <base64-challenge> <base64-signature>)
```

## Technical

Provides ECDSA key generation, public-key export, signing, and verification using OpenSSL. The function name is case-insensitive, but its operation names must be exactly lowercase: `keygen`, `pubkey`, `sign`, or `verify`.

Arguments support double-quoted words regardless of `/xdebug dword`; braced or parenthesized groups are also parsed as individual arguments. Filenames are passed to `fopen()` as supplied after grouping, without tilde expansion. Relative paths use EPIC's working directory. Supply only the arguments shown: extra arguments are not used by these operations, and excessive arguments exceed the fixed parser storage.

## Options and returns

| Operation | Behavior and result |
| --- | --- |
| `keygen` | Generate a key on the `prime256v1` curve, write its unencrypted EC private-key PEM file, and return the Base64-encoded compressed public point. |
| `pubkey` | Load an EC private-key PEM file and return its Base64-encoded compressed public point. |
| `sign` | Decode the challenge from Base64, sign those bytes, and return a Base64-encoded DER ECDSA signature. |
| `verify` | Load the private-key PEM file, decode the challenge and signature, and return `1` if OpenSSL verifies them, otherwise `0`. |

`keygen` opens the destination for writing and **truncates an existing file**. It temporarily uses umask `077` while opening, restores the previous umask, and then sets the file's permissions to `0400` (owner read only). Both creation and permission failures return an empty string with a diagnostic.

`pubkey`, `sign`, and `verify` require the opened file to be a regular file with exactly mode `0400`, including no extra permission or special bits. Files with other modes are rejected. Verification uses a private-key file too; this interface does not accept the exported public-point string or a public-key PEM file. There is no passphrase argument.

The challenge bytes are passed directly to OpenSSL's ECDSA digest interface. This function does not first hash a message or select a digest algorithm. The caller must supply the bytes expected by the protocol. Public-point export is not PEM or a certificate, and the function sends no IRC authentication request itself.

## Returns

Missing input returns an empty string. Unknown operations and missing required operation arguments return an empty string with a diagnostic. Key-load failures and detected signing failures also return an empty string with diagnostics. Verification failure and OpenSSL verification errors both become `0`, rather than distinct error results.

## Restrictions

The function is always registered in current EPIC builds, which require OpenSSL with the EC/ECDSA APIs used by this implementation. It does not offer a curve selector. Imported private keys are read from PEM; the generated-key curve is not separately enforced as a restriction on imported keys.

Base64 decoding uses EPIC's general decoder, which can stop at invalid input and return the decoded prefix. Zero-byte decoding is reported as a transform failure, but the signing and verification wrappers do not independently reject that failure before calling OpenSSL. Supply complete, valid Base64 rather than relying on these operations to validate its syntax.

Some OpenSSL and file-operation results are unchecked: key generation, PEM writing, and file close can fail without being reflected in the returned public-key string. A nonempty `keygen` result therefore does not by itself confirm that a usable key file was saved. Failure to allocate the initial OpenSSL curve object invokes EPIC's fatal error path.

## Examples

With an existing loadable `0400` EC private-key file and a valid Base64 challenge supplied by the caller:

```irc
@ public_point = ecdsatool(pubkey ./authentication.pem)
@ signature = ecdsatool(sign ./authentication.pem $challenge)
@ valid = ecdsatool(verify ./authentication.pem $challenge $signature)
```

Successful verification returns `1`. Generating a new file uses `$ecdsatool(keygen ./new-authentication.pem)`; that call creates or overwrites the named file and returns the public point.

## Source

Registered in [source/functions.c](../../source/functions.c) and implemented in [source/ecdsatool.c](../../source/ecdsatool.c). Argument grouping and Base64 transforms are in [source/ircaux.c](../../source/ircaux.c). [OpenSSL's ECDSA API documentation](https://docs.openssl.org/4.0/man3/ECDSA_sign/) describes the digest input and DER signature format.
