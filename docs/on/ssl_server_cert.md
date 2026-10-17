# on ssl_server_cert

## Synopsis

```text
/on [<noise>]ssl_server_cert "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered after TLS negotiation has completed and EPIC has obtained the peer certificate, before the IRC server connection proceeds through certificate acceptance policy and registration. A completed negotiation does not mean the certificate was accepted: verification errors are reported in the arguments below.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the complete argument string. There are no event-specific options. This event requires a TLS connection using EPIC's OpenSSL implementation; a failed handshake or missing peer certificate does not reach it.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Connection file descriptor, **not a server refnum**. |
| `$1` | URL-encoded certificate subject; use `urldecode($1)` to recover it. |
| `$2` | URL-encoded certificate issuer; use `urldecode($2)` to recover it. |
| `$3` | Certificate public key length in bits. |
| `$4` | OpenSSL verification error selected as the most serious; `0` means no recorded error, `18` means a self-signed leaf certificate, and `62` means a hostname mismatch. |
| `$5` | Negotiated protocol version, such as `TLSv1.2`. |
| `$6` | SHA-1 certificate digest as lowercase hexadecimal bytes separated by colons. |
| `$7` | Hostname mismatch flag: `1` for an error, otherwise `0`. |
| `$8` | Self-signed leaf certificate flag: `1` for an error, otherwise `0`. |
| `$9` | Other verification error flag: `1` for an error, otherwise `0`. |
| `$10` | Any verification error flag: `1` for an error, otherwise `0`. |

The flags can report several error categories at once; `$4` is only one selected error code. The self-signed flag specifically covers a self-signed leaf certificate, not every possible certificate-chain error. Subject and issuer are certificate names, not necessarily just hostnames.

## Default behavior

If the hook permits default handling, EPIC displays the protocol and cipher, certificate subject and issuer, public key length, digest, subject alternative names, and verification results. A suppressing hook, such as a serial-zero `^ssl_server_cert` hook, suppresses those summary lines. Verification diagnostics emitted earlier during negotiation are separate.

Suppressing this output does not accept or reject the certificate. Normally, certificate policy follows this hook: a certificate with no verification errors is accepted; `accept_invalid_ssl_cert` (default on) permits errors limited to hostname mismatch and self-signed leaf certificates. Other errors are not excused by that setting. [On server_ssl_eval](server_ssl_eval.md) provides the later policy event, using a server refnum. For compatibility, a handler that explicitly sets the server's `ssl_accept_cert` field through [serverctl](../functions/serverctl.md) bypasses that normal policy and the later event. Use the later policy event when making an acceptance decision. See [SSL connections](../about/ssl_connections.md) for connection setup.

## Example

Display a concise certificate summary without changing certificate policy:

```text
on ^ssl_server_cert "*" {
    echo TLS $5: subject=$urldecode($1) digest=$6 verification-error=$10;
};
```

## History

The hook originated with the EPIC4-SSL project and first appeared in EPIC4-1.1.3. Parameters `$4` through `$10` appeared in EPIC5-2.1.6.

## Source references

- [source/ssl.c](../../source/ssl.c): `ssl_connected` gathers certificate metadata and invokes the hook; `verify_callback` records verification errors.
- [source/server.c](../../source/server.c): `server_io` invokes certificate processing before acceptance policy and IRC registration.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): `accept_invalid_ssl_cert` and its default.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching and suppression.
